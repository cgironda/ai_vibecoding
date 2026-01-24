# Master Build Plan - Teaching-First Multi-Streaming Platform

The Antigravity-X Hybrid (CodeX Corrections)

This plan keeps the Next.js + LiveKit + Python stack from Antigravity, while
applying CodeX's correctness, failure-mode, and ops requirements.
Note: This file is the original master plan; the supporting specs were split out solely to improve readability.

## 0) Decisions and scope lock
1. **Media plane**: LiveKit (managed cloud by default; self-hosted optional later).
2. **Core stack**:
   - Full stack: Next.js 15 (App Router) + TypeScript.
   - Data: PostgreSQL + Drizzle ORM + Redis + S3-compatible storage.
   - AI/artifacts: Python (FastAPI) + Celery (Redis-backed jobs).
3. **Authoritative state**: `session_state_events` is the source of truth.
   - All state-changing actions must write to the event log first (or in the same transaction) before broadcasting to clients.
4. **Deliverables**:
   - Session lifecycle (schedule, start, join, end).
   - Teaching studio state (scenes, slides, whiteboard).
   - Interaction (chat, Q&A, polls).
   - Artifact pipeline (captions, transcript, chapters/highlights).
   - Replay view.

## 1) Repo structure and tooling
1. **Monorepo layout** (pnpm workspaces):
   - `apps/web`: Next.js (UI + API routes/actions).
   - `apps/artifact-engine`: Python FastAPI (AI/ML worker).
   - `packages/db`: Drizzle schema + migrations.
   - `packages/shared`: shared types, validators, API contracts.
   - `infra`: docker-compose and scripts.
2. **Tooling**: ESLint, Prettier, Vitest, Playwright.
3. **CI**: lint + typecheck + tests.

## 2) Local infrastructure (managed LiveKit)
1. `infra/docker-compose.yml`:
   - Postgres (authoritative DB).
   - Redis (cache + queue backend).
   - MinIO (local S3 for artifacts).
2. `.env.local` template:
   - `DATABASE_URL`, `REDIS_URL`.
   - `S3_ENDPOINT`, `S3_ACCESS_KEY`, `S3_SECRET_KEY`.
   - `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`.

## 3) Database schema (Drizzle ORM)
1. Implement schema from `multistream/docs/master_plan_complement/teaching_first_database_schema.md`:
   - Tenancy: `tenants`, `tenant_domains`.
   - Identity: `users`, `roles`, `user_roles`.
   - Classroom: `courses`, `lessons`, `sessions`, `session_participants`.
   - Event log: `session_state_events`.
   - Studio: `studio_scenes`, `studio_assets`.
   - Interaction: `chat_messages`, `questions`, `polls`.
   - Artifacts: `artifacts`, `recordings`, `replays`, `chapters`, `highlights`.
2. Event log constraints:
   - Unique `(session_id, sequence)` for monotonic ordering.
   - Idempotency key column on write requests to avoid duplicates.
3. Seed data for dev (tenant, teacher, course, lesson).

## 4) Core API foundation (Next.js actions/route handlers)
1. Service-style modules:
   - `IdentityService`, `SessionService`, `StudioService`, `InteractionService`.
2. Validation: Zod schemas shared with UI forms.
3. Error handling: centralized wrapper and typed error responses.
4. Outbox or transactional write pattern:
   - Write to event log first, then publish to WebSocket/LiveKit data channels.

## 5) Identity & auth (teaching-first)
1. Provider: Clerk.
2. RBAC: enforce `user_roles` and session-scoped permissions.
3. Audit logs for role changes and permission overrides.

## 6) Session lifecycle (classroom core)
1. Schedule session: create/update endpoints.
2. Start session:
   - Validate RBAC.
   - Create LiveKit room.
   - Write `session.started` to event log.
3. Join session:
   - Validate enrollment + role.
   - Generate LiveKit access token server-side.
   - Write `participant.joined` to event log.
4. End session:
   - Close LiveKit room / finalize recording.
   - Write `session.ended` to event log.
5. All write endpoints are idempotent with an idempotency key.

## 7) Teaching studio state
1. Scenes CRUD (`studio_scenes`).
2. Real-time updates:
   - Write `scene.changed`, `slide.changed`, `whiteboard.updated` to event log first.
   - Then broadcast via WebSocket/LiveKit data channel for low-latency UI.
3. Clients reconcile from event log on reconnect to prevent drift.

## 8) Interaction service
1. Chat, Q&A, polls:
   - Persist to DB and write event log entry.
   - Broadcast to clients (async-friendly).
2. Failure behavior: if async features fail, class continues (degrade gracefully).

## 9) LiveKit integration
1. Server-side token generation in `apps/web`.
2. Webhooks:
   - `room_finished` -> trigger artifact pipeline.
   - `participant_joined/left` -> emit events to log.
3. Webhook signature validation required.

## 10) Artifact pipeline (Python engine)
1. Queue: Celery with Redis backend.
2. API trigger: Next.js posts a job request to FastAPI, which enqueues.
3. Pipeline stages:
   - Extraction: ASR transcription from recordings.
   - Alignment: align transcript with `session_state_events`.
   - Derivation: chapters, highlights, summaries.
4. Idempotent upserts into `artifacts` tables.
5. Failure tolerance:
   - Artifact failures never halt class.
   - Event capture failure halts class.

## 11) Failure behavior matrix (sync vs async)
Use `multistream/docs/master_plan_complement/feature_backend_mapping.md` as the source of truth. Enforce:
- Scheduling/joining/permissions/recording: synchronous, hard-fail on error.
- Scenes/slides/whiteboard: synchronous with safe fallback to last known state.
- Chat/Q&A/polls/captions/notes/chapters/highlights: asynchronous, degrade gracefully.
- Replays/multistreaming: asynchronous; multistreaming to external destinations is Phase 2.
- CTAs/QR codes: synchronous with fallback to static CTA.

## 12) Replay service & UX
1. API:
   - Fetch replay metadata, transcript, chapters, highlights.
   - Generate signed URLs for recordings.
2. UI:
   - Timeline + chapter markers.
   - Transcript panel with search.
   - Highlight reel.
3. Replay state derived from event log + artifacts.

## 13) Security, compliance, and auditability
1. Audit log coverage: every state change is captured in `session_state_events`.
2. Tenant isolation: enforce `tenant_id` filters on all queries.
3. Secrets: LiveKit keys and S3 keys never exposed to client.
4. Data protection:
   - Encryption at rest.
   - TLS in transit.
   - Access logs for replays and recordings.
5. Retention and deletion policies for transcripts/recordings.

## 14) Idempotency and sequencing rules
1. Every write endpoint accepts an idempotency key.
2. `session_state_events.sequence` increments monotonically per session.
3. Consumers must tolerate duplicate events safely.
4. All projections/replay views rebuildable from event log + raw media.

## 15) Testing strategy
1. Unit tests:
   - Permission checks, idempotency logic.
2. Integration tests:
   - Session start/join/end.
   - Scene switch -> event log write -> broadcast.
3. Worker tests:
   - Artifact retries and idempotent writes.
4. E2E tests (Playwright):
   - Teacher starts class, student joins, chat works.
5. Failure-mode tests:
   - Async service down should not stop class.

## 16) Observability and ops
1. Structured logs (JSON) with request IDs.
2. Metrics:
   - Join success rate, session start latency, job queue depth.
3. Error tracking (Sentry).
4. Runbooks:
   - Session start failure.
   - LiveKit outage or webhook failures.
   - Artifact pipeline failure.

## 17) Milestone checkpoints
1. M1: Monorepo + infra + auth + core schema.
2. M2: Session lifecycle with LiveKit (managed).
3. M3: Studio state + interaction features.
4. M4: Artifact pipeline with captions/transcripts.
5. M5: Replay UX and compliance hardening.

## 18) Definition of done
1. All core flows match `multistream/docs/master_plan_complement/master_specification.md`.
2. Feature ownership and failure behavior match `multistream/docs/master_plan_complement/feature_backend_mapping.md`.
3. Schema matches `multistream/docs/master_plan_complement/teaching_first_database_schema.md`.
4. Artifact pipeline matches `multistream/docs/master_plan_complement/teaching_first_artifact_pipeline.md`.
5. Local stack runs with managed LiveKit and passing tests.

Note: Restream support docs are Phase 2 reference only and not required for MVP.
