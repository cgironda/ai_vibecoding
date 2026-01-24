# Product Requirements Document – Teaching‑First Multi‑Streaming Platform (Antigravity‑X Hybrid)

## 1. Purpose and Vision
This project aims to build a teaching‑first multi‑streaming platform leveraging a Next.js + LiveKit + Python stack. The platform must enable educators to schedule and run live sessions, manage interactive studios, capture artifacts (captions, transcripts, chapters, highlights), and provide replay experiences. Emphasis is on deterministic state management via event sourcing, strict role‑based access control (RBAC), and graceful degradation for asynchronous features.  

## 2. Goals
- Deliver a stable session lifecycle: scheduling, starting, joining, ending sessions.  
- Provide a studio environment where educators manage scenes, slides, and whiteboards in real‑time.  
- Enable interaction features: chat, Q&A, polls with persistence and low‑latency delivery.  
- Build an artifact pipeline producing transcripts, chapters, highlights, and replay files.  
- Ensure compliance, security, auditability, and tenant isolation.  
- Achieve operational readiness with monitoring, logging, and testing to support continuous delivery.  

## 3. Scope and Decisions
- **Media plane**: LiveKit cloud by default; self‑hosted optional later.  
- **Core stack**: Next.js 15 (App Router) with TypeScript; PostgreSQL + Drizzle ORM + Redis + S3‑compatible storage; Python FastAPI with Celery for AI/artifact tasks.  
- **State management**: `session_state_events` table is the single source of truth. All mutations must record events before broadcasting to clients.  
- **Deliverables**: session lifecycle, studio state management, interaction services, artifact pipeline, and replay UX.  
- **Out of scope (MVP)**: external multistream destinations (YouTube/Twitch), advanced analytics, and monetization features; these may be considered in later phases.  

## 4. System Architecture
### 4.1 Monorepo Structure
- `apps/web`: Next.js app with UI and API routes/actions.  
- `apps/artifact-engine`: Python FastAPI application for AI/ML tasks.  
- `packages/db`: Drizzle schema definitions and migrations.  
- `packages/shared`: Shared TypeScript types, validators, API contracts.  
- `infra`: Docker‑Compose and scripts for local infra.  

### 4.2 Local Infrastructure
- **Database**: PostgreSQL as the authoritative database; schema defined in Drizzle.  
- **Cache & queues**: Redis used for caching and as a Celery backend.  
- **Object storage**: MinIO (S3‑compatible) for artifact storage.  
- **LiveKit**: managed cloud with API key/secret; optional self‑host later.  
- Environment variables must be templated (`.env.local`) for local development.  

### 4.3 Database Schema Overview
- Tenancy: `tenants`, `tenant_domains`.  
- Identity & RBAC: `users`, `roles`, `user_roles`.  
- Classroom: `courses`, `lessons`, `sessions`, `session_participants`.  
- Event Log: `session_state_events` with `(session_id, sequence)` unique and idempotency keys.  
- Studio: `studio_scenes`, `studio_assets`.  
- Interaction: `chat_messages`, `questions`, `polls`.  
- Artifacts: `artifacts`, `recordings`, `replays`, `chapters`, `highlights`.  
- Seed data for development: default tenant, teacher, course, lesson.  

### 4.4 Integration Points
- **LiveKit**: server‑side token generation, event webhooks (`room_finished`, `participant_joined/left`). Webhook signatures must be validated.  
- **Artifact Engine**: Next.js posts job requests to FastAPI, which enqueues Celery tasks. Tasks extract ASR transcription, align with events, derive chapters/highlights, and upsert results into the artifacts tables.  

## 5. Functional Requirements
### 5.1 Identity & Authentication
- Integrate with Clerk for authentication.  
- Implement RBAC enforcing `user_roles` and session‑scoped permissions.  
- Maintain audit logs for role changes and permission overrides.  
- Provide secure, signed LiveKit access tokens to authorized participants.  

### 5.2 Session Lifecycle
- **Schedule Session**: create/update endpoints storing session metadata and schedule.  
- **Start Session**: validate RBAC, create LiveKit room, write `session.started` event, return session start acknowledgment.  
- **Join Session**: validate user enrollment and role, generate LiveKit token, write `participant.joined` event.  
- **End Session**: close LiveKit room, finalize recording, write `session.ended` event.  
- All endpoints must accept an idempotency key; duplicate requests must not duplicate state.  

### 5.3 Teaching Studio State
- Provide CRUD endpoints for scenes (`studio_scenes`) and assets (`studio_assets`).  
- Events such as `scene.changed`, `slide.changed`, `whiteboard.updated` must be written to the event log and then broadcast via WebSockets/LiveKit data channels.  
- Clients must reconcile their state from the event log upon reconnect to avoid drift.  

### 5.4 Interaction Services
- Chat, Q&A, and polls must persist messages/questions/poll responses to the database and record corresponding events.  
- Real‑time broadcast to connected clients through WebSockets/LiveKit.  
- Failure of these async features must not interrupt the session; degrade gracefully.  

### 5.5 Artifact Pipeline
- Tasks: extraction (ASR transcription), alignment with `session_state_events`, derivation of chapters, highlights, and summaries.  
- Idempotent upserts into artifact‑related tables; duplication avoided by idempotency keys.  
- Pipeline failures must not halt classes; event capture failure is critical and must halt the class.  

### 5.6 Replay Service & UX
- API endpoints for replay metadata, transcripts, chapters, highlights, and signed URLs for recordings.  
- UI must provide timeline with chapter markers, a searchable transcript panel, and a highlight reel.  
- Replay state derived from the event log and artifact outputs.  

## 6. Non‑Functional Requirements
- **Security**: enforce tenant isolation via `tenant_id` filters; do not expose LiveKit or S3 secrets; use encryption at rest and TLS in transit; implement access logs for replays and recordings; define retention/deletion policies for transcripts and recordings.  
- **Idempotency & Ordering**: all write endpoints must accept an idempotency key; `session_state_events.sequence` increments monotonically per session; downstream consumers must handle duplicate events gracefully; all projections must be rebuildable from the event log and raw media.  
- **Reliability**: synchronous operations (scheduling, joining, permissions, recording) must hard‑fail on error; scenes/slides/whiteboard updates must fail fast with fallback to last known state; asynchronous features (chat, polls, captions, notes, chapters, highlights) must degrade gracefully; replays or multistreaming (if enabled) may be delayed; CTAs/QR codes synchronous with fallback to static CTA.  
- **Observability**: structured JSON logging with request IDs; metrics for join success rate, session start latency, job queue depth; error tracking with Sentry or equivalent; runbooks for session start failure, LiveKit outage/webhook failure, and artifact pipeline failure.  
- **Compliance**: alignment with definitions in `multistream/docs/master_plan_complement/master_specification.md`, `multistream/docs/master_plan_complement/feature_backend_mapping.md`, `multistream/docs/master_plan_complement/teaching_first_database_schema.md`, `multistream/docs/master_plan_complement/teaching_first_artifact_pipeline.md`; ensure local stack runs with managed LiveKit and passes all tests.  

## 7. Testing Strategy
- **Unit Tests**: permission checks, idempotency logic, schema validations.  
- **Integration Tests**: session start/join/end flows; scene switch event logging and broadcast; LiveKit token generation.  
- **Worker Tests**: artifact pipeline retries and idempotent upserts.  
- **End‑to‑End (Playwright)**: teacher starts class, student joins, chat and polls function correctly.  
- **Failure‑Mode Tests**: simulate async service downtime and confirm class continues without blocking.  

## 8. Milestones and Roadmap
- **M1**: Establish monorepo, local infrastructure, authentication, and core database schema.  
- **M2**: Implement session lifecycle with LiveKit integration.  
- **M3**: Develop studio state management and interaction features.  
- **M4**: Deliver artifact pipeline with captioning and transcript generation.  
- **M5**: Build replay UX and finalize compliance hardening.  

Each milestone should include complete testing and documentation; progress to the next milestone only when preceding deliverables are stable.  

## 9. Risks and Considerations
- **Event sourcing complexity**: correctness depends on strict adherence to event ordering and idempotency. Developers must resist shortcuts that bypass the event log.  
- **LiveKit dependency**: reliance on managed LiveKit could become a single point of failure; plan for self‑hosting fallback and monitor API quotas.  
- **Artifact processing latency**: ASR transcription and alignment may introduce delays for replays. Set expectations that replays may be available after processing.  
- **Scaling**: concurrency in sessions and artifact jobs must be tested under load; Redis and Postgres tuning required.  
- **Security**: any leakage of secrets or cross‑tenant data could be catastrophic; implement thorough penetration testing.  

## 10. Definition of Done
- All core flows conform to the master specification, feature‑backend mapping, and database schema documents.  
- Failure behavior aligns with defined synchronous/asynchronous rules.  
- Local stack operates with managed LiveKit and all tests pass.  
- CI pipeline enforces linting, type checking, and test execution.  
