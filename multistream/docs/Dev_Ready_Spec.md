# Dev‑Ready Specification – Teaching‑First Multi‑Streaming Platform

Here’s how engineers would receive the `multistream/docs/PRD.md` document:

## 1. Overview
This specification translates the high‑level PRD into actionable development tasks and structures. It defines the module boundaries, data models, API contracts, and operational constraints for implementing a teaching‑first multi‑streaming platform using a Next.js + LiveKit + Python stack.
Supporting docs: `multistream/docs/API_Event_Contracts.md` (API and event contracts), `multistream/docs/UX_Spec.md` (UX flows), and `multistream/docs/Ops_Runbook.md` (operations and incident response).

## 2. Architecture

### 2.1 Module Breakdown
- **Web Application (`apps/web`)**  
  - Next.js 15 (App Router) with TypeScript for UI and API routes/actions.  
  - Handles identity/auth, session lifecycle, studio controls, interaction services, and integration with LiveKit.  
  - Uses Zod schemas for request/response validation.  
- **Artifact Engine (`apps/artifact-engine`)**  
  - Python FastAPI service for artifact processing jobs.  
  - Enqueues tasks to Celery backed by Redis; performs transcription, alignment, and derivation.  
  - Provides endpoints triggered by webhooks and internal job requests.  
- **Shared Packages (`packages/shared`, `packages/db`)**  
  - Contains Drizzle schema definitions, migrations, TypeScript types, validators, and API contracts.  
  - Centralizes business logic for RBAC and event log handling.  
- **Infrastructure (`infra`)**  
  - Docker‑Compose for local services (Postgres, Redis, MinIO).  
  - Managed LiveKit configured via environment variables; self‑host optional later.  

### 2.2 Data Flow
1. **Session Start**: Client calls `/api/sessions/start` → server validates RBAC → creates LiveKit room → writes `session.started` event → responds with LiveKit token and session info.  
2. **Participant Join**: Client calls `/api/sessions/join` → server validates enrollment and role → writes `participant.joined` event → issues LiveKit token.  
3. **Studio Interaction**: Client triggers scene/slide/whiteboard updates via API → server writes events (`scene.changed`, etc.) → publishes through WebSocket/LiveKit data channels.  
4. **Artifact Processing**: LiveKit webhook `room_finished` triggers a POST to FastAPI → artifact engine enqueues extraction/alignment/derivation tasks → upserts artifacts and signals completion.  
5. **Replay Access**: Client fetches replay metadata and signed URLs via `/api/replays/:sessionId` → server assembles data from `session_state_events` and artifacts tables.  

## 3. Data Models

### 3.1 Core Entities
- **Tenant**  
  - `id` (UUID) – primary key.  
  - `name` (string).  
- **User**  
  - `id` (UUID).  
  - `email` (string).  
  - `hashed_password` (string).  
  - `tenant_id` (UUID, FK tenants).  
- **Role**  
  - `id` (UUID).  
  - `tenant_id` (UUID, FK tenants).  
  - `name` (enum: "teacher", "student", "assistant", "admin").  
- **UserRole**  
  - `user_id` (UUID, FK users).  
  - `role_id` (UUID, FK roles).  
  - `scope` (enum: "tenant", "course", "session").  
  - `scope_id` (UUID, nullable based on scope).  
- **SessionParticipant**  
  - `session_id` (UUID, FK sessions).  
  - `user_id` (UUID, FK users).  
  - `role` (enum: "teacher", "student", "assistant").  
  - `join_time` (timestamp with TZ).  
  - `leave_time` (timestamp with TZ).  
  - `permissions` (JSONB).  
- **Course / Lesson / Session**  
  - Course: `id`, `tenant_id`, `title`.  
  - Lesson: `id`, `course_id`, `title`, `order`.  
  - Session: `id`, `lesson_id`, `scheduled_start`, `scheduled_end`, `status`.  
- **SessionStateEvent**  
  - `id` (UUID).  
  - `session_id` (UUID).  
  - `sequence` (integer, monotonic per session).  
  - `event_type` (string; e.g., "session.started", "participant.joined", etc.).  
  - `event_payload` (JSONB).  
  - `created_at` (timestamp with TZ).  
  - `idempotency_key` (string).  
- **StudioScene / StudioAsset**  
  - Scene: `id`, `session_id`, `name`, `layout`, `metadata`.  
  - Asset: `id`, `scene_id`, `type` (slide, whiteboard), `content_url`.  
- **ChatMessage / Question / Poll**  
  - Message: `id`, `session_id`, `user_id`, `content`, `created_at`.  
  - Question: similar structure with upvote counts.  
  - Poll: `id`, `session_id`, `prompt`, `options`, `responses`.  
- **Artifact / Recording / Replay**  
  - Artifact: `id`, `session_id`, `type` (transcript, chapters, highlights), `data_url`.  
  - Recording: `id`, `session_id`, `file_url`, `created_at`.  
  - Replay: `id`, `session_id`, `metadata_json`, `created_at`.  

### 3.2 Constraints
- `SessionStateEvent` must enforce unique `(session_id, sequence)`.  
- `SessionStateEvent` must enforce unique `(session_id, idempotency_key)` for dedupe.  
- All write endpoints must supply an `idempotency_key`; duplicate keys must result in no-op.  
- Foreign keys must include `tenant_id` to enforce isolation.  

## 4. API Specifications
See `multistream/docs/API_Event_Contracts.md` for canonical payloads, headers, and error schema.  

### 4.1 Authentication
- **POST /api/auth/login**: accepts `{ email, password }`; returns JWT or session token; uses Clerk.  
- **POST /api/auth/logout**: invalidates session.  
- **GET /api/auth/me**: returns authenticated user and roles.  

### 4.2 Session Management
- **POST /api/sessions**: create or schedule a session.  
  - Request: `{ lessonId, scheduledStart, scheduledEnd, idempotencyKey }`.  
  - Response: session record.  
- **POST /api/sessions/start**: start a session.  
  - Request: `{ sessionId, idempotencyKey }`.  
  - Actions: RBAC check → LiveKit room creation → event log write.  
- **POST /api/sessions/join**: join a session.  
  - Request: `{ sessionId, idempotencyKey }`.  
  - Response: LiveKit token and participant info.  
- **POST /api/sessions/end**: end a session.  
  - Request: `{ sessionId, idempotencyKey }`.  
  - Actions: close LiveKit room, finalize recording, write event.  

### 4.3 Studio and Interaction
- **POST /api/studio/scenes**: create scene (teacher only).  
- **PATCH /api/studio/scenes/:sceneId**: update scene (layout, assets).  
- **POST /api/studio/assets**: upload slide or whiteboard asset; returns content URL.  
- **POST /api/chat**: send chat message.  
- **POST /api/questions**: submit question.  
- **POST /api/polls**: create poll; subsequent endpoints handle responses and tally.  

### 4.4 Replay and Artifacts
- **GET /api/replays/:sessionId**: fetch replay metadata, chapters, highlights, signed recording URLs.  
- **POST /api/artifacts/process**: internal endpoint for artifact engine; triggers extraction/alignment/derivation based on recording URL.  

### 4.5 Webhooks
- **POST /api/webhooks/livekit**: handle LiveKit events.  
  - Verify signature via secret.  
  - Recognize `room_finished` to trigger artifact jobs; `participant_joined/left` to write events.  

## 5. Error Handling and Idempotency
- All POST/PATCH endpoints must accept `Idempotency-Key` (preferred) or `idempotencyKey` in the body. Duplicate requests must return the same response or a 409 indicating prior completion.  
- Use centralized error format: `{ code, message, details? }`.  
- Synchronous endpoints must hard-fail for invalid input, permission errors, and service failures; asynchronous failures should return success to caller and log internally.  

## 6. Testing and Quality Assurance
- Implement unit tests covering RBAC logic, event ordering, and idempotency enforcement.  
- Integration tests should simulate session lifecycle, studio updates, and interaction flows using a controlled LiveKit instance.  
- End‑to‑end tests (Playwright) must automate the teacher and student experiences, asserting UI updates correspond to event log writes.  
- Load tests should evaluate concurrency in sessions and artifact processing, ensuring Redis and Postgres do not become bottlenecks.  

## 7. Deployment and Ops
- Continuous Integration must run linting (ESLint, Prettier), type checking (TypeScript, MyPy), unit and integration tests.  
- Continuous Deployment should deploy to staging and production using infrastructure‑as‑code (e.g., Terraform) with environment secrets stored securely.  
- Monitoring dashboards must expose metrics (join success rate, session latency, job queue depth) and integrate with alerting systems.  
- Runbooks for session start failures, LiveKit outages, webhook failures, and artifact pipeline failures must be documented and tested.  

## 8. Risks and Mitigations
- **Event Log Integrity**: Strict database constraints and transactional writes are required; missing events break projections. Mitigation: wrap writes and broadcasts in a single transaction; audit event counts.  
- **Third‑Party Dependency**: LiveKit downtime halts live sessions. Mitigation: prepare self‑hosted fallback and monitor third‑party status; provide clear error messages to users.  
- **Performance Bottlenecks**: Artifact processing may consume CPU; concurrency could overload Redis. Mitigation: rate‑limit processing, scale worker instances horizontally, monitor queue depths.  
- **Security Breaches**: Unauthorized access or data leakage is unacceptable. Mitigation: enforce RBAC at every layer, use TLS everywhere, rotate keys regularly, and perform regular penetration testing.  

## 9. Deliverables Checklist
- [ ] Monorepo set up with `apps`, `packages`, and `infra`.  
- [ ] Drizzle schema implemented and migrations created.  
- [ ] Core authentication and RBAC modules with audit logging.  
- [ ] Session lifecycle endpoints with event sourcing.  
- [ ] Studio scene management and real‑time broadcasting.  
- [ ] Interaction services (chat, Q&A, polls) with graceful degradation.  
- [ ] Artifact processing engine with ASR, alignment, and derivation.  
- [ ] Replay API and UI with chapters and highlights.  
- [ ] Testing suites (unit, integration, E2E) passing.  
- [ ] Observability stack (logging, metrics, alerting) operational.  

## 10. Acceptance Criteria
- Every state‑changing operation is logged in `session_state_events` and can be replayed to reconstruct session state.  
- Idempotent keys prevent duplicate side effects across all write operations.  
- Unauthorized users cannot schedule, start, join, or end sessions.  
- Scenes, slides, and whiteboard updates propagate in under 200 ms for participants on the same region.  
- Replay pages load transcript and chapters within 2 s and provide signed URL access to recordings.  
- Artifact failures do not block live sessions and are retried until completion or manual intervention.  
