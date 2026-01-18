# Master Build Plan - Teaching-First Multi-Streaming Platform
#
# "The Antigravity-X Hybrid"
#
# This plan merges the rigorous operational roadmap of the 'CodeX Platform' with the
# superior, AI-ready T3+ Stack (Next.js, LiveKit, Python) of the 'Antigravity Plan'.

## 0) Decisions and Scope Lock
1.  **Media Plane**: **LiveKit** (Managed Cloud or Self-Hosted).
2.  **Core Stack**:
    *   **Full Stack**: **Next.js 15** (App Router) + TypeScript.
    *   **Data**: **PostgreSQL** + **Drizzle ORM** + **Redis** + **S3**.
    *   **AI/Artifacts**: **Python** (FastAPI) + **BullMQ** (via Redis bridge).
3.  **Deliverables**:
    *   Session lifecycle (schedule, start, join, end).
    *   Teaching studio state (scenes, slides, whiteboard).
    *   Interaction (chat, Q&A, polls).
    *   Artifact pipeline (captions, transcript, chapters found by AI).
    *   Replay view.

## 1) Repo Structure and Tooling
1.  **Monorepo Layout** (pnpm workspaces):
    *   `apps/web`: Next.js 15 (Teacher/Student UI + API Routes).
    *   `apps/artifact-engine`: Python FastAPI (AI/ML Worker).
    *   `packages/db`: Drizzle Schema & Migrations (Shared).
    *   `packages/ui`: Shared UI Components (shadcn/ui).
    *   `infra`: Docker-compose and K8s/Terraform scripts.
2.  **Tooling**: ESLint, Prettier, Jest/Vitest.
3.  **CI**: GitHub Actions (Lint + Typecheck + Test).

## 2) Local Infrastructure
1.  `infra/docker-compose.yml`:
    *   **Postgres**: Authoritative DB.
    *   **Redis**: Cache & Message Bus (BullMQ).
    *   **MinIO**: Local S3 for artifacts.
    *   **LiveKit Server**: (Optional) or use LiveKit Cloud dev instance.
2.  `.env.local` template:
    *   `DATABASE_URL`, `REDIS_URL`.
    *   `S3_ENDPOINT`, `S3_ACCESS_KEY`, `S3_SECRET_KEY`.
    *   `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`.

## 3) Database Schema (Drizzle ORM)
1.  Initialize **Drizzle ORM** in `packages/db`.
2.  Implement Schema (`teaching_first_database_schema.md`):
    *   **Tenancy**: `tenants`, `tenant_domains`.
    *   **Identity**: `users`, `roles`, `user_roles`.
    *   **Classroom**: `courses`, `lessons`, `sessions`, `session_participants`.
    *   **Event Log**: `session_state_events` (The Immutable Core).
    *   **Studio**: `studio_scenes`, `studio_assets`.
    *   **Interaction**: `chat_messages`, `questions`, `polls`.
    *   **Artifacts**: `artifacts`, `recordings`, `replays`.
3.  Seed data script (Teacher, Course, Lesson).

## 4) Core API Foundation (Next.js Actions)
*Unlike NestJS, we use Next.js Server Actions & Route Handlers for direct, type-safe access.*
1.  **Auth Layer**: Service Pattern for `IdentityService`.
2.  **Orchestrator Layer**: Service Pattern for `SessionService`.
3.  **Validation**: Zod schemas (shared with frontend forms).
4.  **Error Handling**: Centralized `safeAction` wrapper for Server Actions.

## 5) Identity & Auth (Teaching-First)
1.  **Provider**: Managed Auth (e.g., Clerk or Auth.js/NextAuth).
2.  **RBAC**: Custom Middleware enforcing `user_roles`.
    *   Teachers can START/END.
    *   Students can JOIN/INTERACT.
3.  **Session Scope**: Permissions verified against `session_participants`.

## 6) Session Lifecycle (The Classroom Core)
1.  **Schedule**:
    *   `POST /api/sessions` (or Server Actions).
2.  **Start (Teacher)**:
    *   Validate RBAC.
    *   Create LiveKit Room.
    *   **Write Event**: `session.started` (Sequence 1).
3.  **Join (Student)**:
    *   Validate Enrollment.
    *   Generate LiveKit Token (Server-side).
    *   **Write Event**: `participant.joined`.
4.  **End**:
    *   Close LiveKit Room.
    *   **Write Event**: `session.ended`.

## 7) Teaching Studio State
*Real-time state synchronization.*
1.  **Scenes**: CRUD for `studio_scenes`.
2.  **Live State**:
    *   Use **LiveKit Data Messages** for sub-100ms latency (Scene Switch, Slide Advance).
    *   Backup: Persist to `session_state_events` (Async).

## 8) Interaction Service
1.  **Chat/Q&A/Polls**:
    *   Persist to DB `chat_messages`.
    *   Emit via LiveKit Data Channel for instant UI update.
    *   **Write Event**: `chat.message` (for audit/replay).

## 9) LiveKit Integration
1.  **Server-Side**: Token generation in `apps/web`.
2.  **Webhooks**:
    *   Handle `room_finished` -> Trigger **Artifact Pipeline**.
    *   Handle `track_published` -> Update layouts.

## 10) Artifact Pipeline (Python Engine)
*This is where we diverge from Node.js to use the best AI tools.*
1.  **Queue**: Next.js pushes job to Redis (BullMQ).
2.  **Engine** (`apps/artifact-engine`):
    *   **FastAPI** worker listening to Redis.
3.  **Stage 1: Extraction**:
    *   Download Recording (S3).
    *   **Whisper** (or API) for Transcription.
4.  **Stage 2: Alignment**:
    *   Align Transcript timestamps with `session_state_events`.
5.  **Stage 3: Derivation (LLM)**:
    *   Generate Chapters, Highlights, Summaries.
6.  **Persistence**: Write `artifacts` to Postgres.

## 11) Replay Service & UX
1.  **Player**: Custom Video Player (Remotion or standard Video tag).
2.  **Hydration**:
    *   Fetch `session_state_events` to "replay" the specific layout/slides at each timestamp.
    *   Overlay Chat/Polls at correct times.

## 12) Security & Compliance
1.  **Audits**: `session_state_events` is the audit log.
2.  **Data Isolation**: Ensure tenant_id filters on ALL queries.
3.  **Secrets**: DO NOT expose LiveKit keys or S3 keys to client.

## 13) Testing Strategy
1.  **Unit**: Vitest for Logic/Services.
2.  **Integration**: Test the "Next.js Action -> DB Write -> Event Log" flow.
3.  **E2E**: Playwright.
    *   Teacher Login -> Start Class -> Check LiveKit Token.

## 14) Milestone Checkpoints
1.  **M1 (Foundation)**: Monorepo, Docker, DB Schema, Auth.
2.  **M2 (Lifecycle)**: Start/Join/End Class with LiveKit Video.
3.  **M3 (Studio)**: Scene Switching, Slides, Chat.
4.  **M4 (The Brain)**: Python Pipeline + Transcription.
5.  **M5 (Replay)**: The "Perfect Replay" viewer.
