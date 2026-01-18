# Concrete Build Plan - Teaching-First Multi-Streaming Platform (Managed LiveKit)
#
# This is a step-by-step, buildable plan derived from the master specification and
# companion documents. It keeps the LiveKit media plane managed and local services
# containerized for development/testing.

## 0) Decisions and scope lock
1. Confirm managed LiveKit (LiveKit Cloud) for the media plane.
2. Confirm stack:
   - Web: Next.js + TypeScript
   - API: NestJS + TypeScript
   - Data: Postgres + Redis + S3-compatible object storage
   - Jobs: BullMQ or Temporal
3. Confirm initial deliverables:
   - Session lifecycle (schedule, start, join, end)
   - Teaching studio state (scenes, slides, whiteboard)
   - Interaction (chat, Q&A, polls)
   - Artifact pipeline (captions, transcript, chapters/highlights)
   - Replay view

## 1) Repo structure and tooling
1. Create a monorepo layout (keeps service boundaries modular):
   - `apps/web` (Next.js)
   - `apps/api` (NestJS)
   - `apps/worker` (artifact pipeline)
   - `packages/shared` (types, utils, API contracts)
   - `infra` (docker-compose, scripts)
2. Choose package manager (pnpm recommended for monorepos).
3. Add TypeScript config, linting, and test tooling (eslint, prettier, jest).
4. Add basic CI pipeline (lint + typecheck + test).

## 2) Local infrastructure (managed LiveKit)
1. Add `infra/docker-compose.yml` for:
   - Postgres (authoritative DB)
   - Redis (cache/queues)
   - MinIO (S3-compatible object storage)
2. Add `.env.local` template for:
   - `DATABASE_URL`, `REDIS_URL`
   - `S3_ENDPOINT`, `S3_ACCESS_KEY`, `S3_SECRET_KEY`
   - `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`
3. Verify services start locally; confirm DB + Redis connectivity.

## 3) Database schema and migrations
1. Pick ORM (Prisma or Drizzle) and initialize migrations.
2. Implement schema from `teaching_first_database_schema.md`, starting with:
   - `tenants`, `tenant_domains`
   - `users`, `roles`, `user_roles`
   - `courses`, `lessons`, `sessions`, `session_participants`
3. Implement the event-sourced core:
   - `session_state_events` with monotonic `sequence` per session
   - Unique constraint on `(session_id, sequence)`
4. Add studio state tables:
   - `studio_scenes`, `studio_assets`
5. Add interaction tables:
   - `chat_messages`, `questions`, `polls`
6. Add artifact tables:
   - `artifacts`, `recordings`, `replays`, `chapters`, `highlights`
7. Add required indexes and foreign keys.
8. Seed minimal data for dev (tenant, teacher, sample course).

## 4) API service (NestJS) foundation
1. Initialize NestJS app with modular structure:
   - Identity & Roles module
   - Classroom Orchestrator module
   - Teaching Studio State module
   - Interaction module
   - Replay module
   - Artifact module (read-only initially)
2. Add validation layer (zod or class-validator) for API payloads.
3. Implement centralized error handling and request tracing.

## 5) Identity, roles, and auth (teaching-first rules)
1. Integrate OAuth2/OIDC provider (Auth0/Clerk/Keycloak).
2. Implement JWT validation at the API gateway.
3. Build RBAC guards using `roles` + `user_roles` + session-scoped permissions.
4. Expose admin endpoints:
   - Create users
   - Assign roles
   - Manage tenant domains

## 6) Session lifecycle (classroom core)
1. Implement scheduling:
   - Create session
   - Update schedule
2. Implement start session:
   - Validate permissions
   - Create LiveKit room (managed)
   - Emit `session.started` event (event log)
3. Implement join flow:
   - Validate enrollment + role
   - Generate LiveKit access token server-side
   - Emit `participant.joined` event
4. Implement end session:
   - End LiveKit room / finalize recording
   - Emit `session.ended` event
5. Ensure all write endpoints append to `session_state_events`.

## 7) Teaching Studio State (scenes, slides, whiteboard)
1. Implement scene CRUD in API:
   - Create scene
   - Switch active scene
2. Store state in `studio_scenes` + `studio_assets`.
3. Emit events:
   - `scene.changed`, `slide.changed`, `whiteboard.updated`
4. Provide WebSocket channel for live state updates.

## 8) Interaction service (chat, Q&A, polls)
1. Implement chat message creation and retrieval.
2. Implement Q&A:
   - Ask question, answer question, mark as resolved
3. Implement polls:
   - Create poll, vote, end poll
4. Emit corresponding events to `session_state_events`.
5. Mark failure behavior as async (degrade gracefully).

## 9) LiveKit integration (managed)
1. Server-side token generation:
   - Use `LIVEKIT_API_KEY`/`LIVEKIT_API_SECRET`
2. Store room metadata (session_id, tenant_id) for audit.
3. Register LiveKit webhooks:
   - Participant joined/left
   - Recording finalized
4. On webhook receipt:
   - Validate signature
   - Emit system events to the event log

## 10) Artifact pipeline (worker service)
1. Initialize worker app with BullMQ or Temporal.
2. Stage 1: Live event capture
   - Subscribe to event log stream
3. Stage 2: Raw extraction
   - Generate transcript (ASR) from recording audio
   - Store raw outputs in object storage
4. Stage 3: Temporal alignment
   - Align transcript with slides/whiteboard/events
5. Stage 4: Knowledge derivation
   - Generate chapters, highlights, notes
6. Persist artifacts into DB with idempotent upserts.
7. Failure tolerance:
   - Event capture failure halts class
   - Artifact failures degrade gracefully

## 11) Replay service
1. Build API endpoints:
   - Fetch replay metadata
   - Fetch transcript, chapters, highlights
   - Generate signed URLs to recordings
2. Build replay UI:
   - Timeline with chapters
   - Transcript panel with search
   - Highlights carousel

## 12) Frontend (Next.js) core flows
1. Auth flow and role-based routing.
2. Teacher UI:
   - Schedule session
   - Start session
   - Scene switching
   - Q&A moderation
3. Student UI:
   - Join session
   - View stream
   - Chat/Q&A/Polls
4. Assistant UI:
   - Moderation tools
5. Integrate LiveKit React SDK for media.
6. Integrate WebSocket updates for state changes.

## 13) Security, compliance, and auditability
1. Enforce RBAC on all endpoints.
2. Add audit logs for permission changes and replay access.
3. Encrypt sensitive data at rest; never expose secrets to clients.
4. Implement data retention and deletion policies.

## 14) Testing plan (local)
1. Unit tests:
   - Event log writers
   - Permission checks
2. Integration tests:
   - Start session -> join -> end
   - Scene switch -> event log entry -> client update
3. Worker tests:
   - Artifact job retries and idempotency
4. E2E tests (Playwright):
   - Teacher starts class, student joins, chat works
5. Local load test targets:
   - Small concurrency (10-50 participants)

## 15) Observability and ops
1. Add structured logging (JSON).
2. Add metrics (request latency, join success rate, job queue depth).
3. Add error tracking (Sentry).
4. Prepare runbooks for:
   - Session start failure
   - LiveKit errors
   - Artifact pipeline failures

## 16) Milestone checkpoints
1. M1 - Core classroom:
   - Schedule -> start -> join -> end with LiveKit managed
   - Event log writes for all state changes
2. M2 - Teaching studio + interaction:
   - Scenes, slides, whiteboard, chat, Q&A, polls
3. M3 - Artifact pipeline:
   - Captions + transcript + chapters + highlights
4. M4 - Replay UX:
   - Full replay with artifacts
5. M5 - Security/compliance:
   - RBAC hardening + audit logs + retention rules

## 17) Definition of done
1. All core flows match `master_specification.md` behavior.
2. Feature ownership aligns with `feature_backend_mapping.md`.
3. Database schema matches `teaching_first_database_schema.md`.
4. Artifact pipeline matches `teaching_first_artifact_pipeline.md`.
5. Local stack runs with managed LiveKit integration and passing tests.
