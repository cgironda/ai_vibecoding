# Prompt for Building the Teaching-First Multi-Streaming Platform
#

Initialize the **Teaching-First Multi-Streaming Platform** using the following files as authoritative source of truth:

- multistream/master_specification.md
- multistream/feature_backend_mapping.md
- multistream/teaching_first_database_schema.md
- multistream/teaching_first_artifact_pipeline.md

Stack (managed LiveKit):
- Web clients: Next.js (React + TypeScript)
- Backend control plane: NestJS (TypeScript)
- Data: PostgreSQL + Redis + Object Storage (S3-compatible)
- Background jobs: BullMQ or Temporal
- Media plane: LiveKit Cloud (managed) for SFU/recording; separate service
- Optional search: OpenSearch/Meilisearch

Deliver a full, buildable design and implementation plan with code-level detail. The platform must honor teaching-first rules, event-sourced core, and the sync/async and failure behavior specified in the docs.

What to produce (in this order):
1) Architecture blueprint
   - Service boundaries (Identity & Roles, Classroom Orchestrator, Teaching Studio State, Interaction Service, Artifact Generator, Replay UX, Integration Gateway)
   - Sync vs async boundaries and failure behavior
   - Event log as source of truth
   - Media plane integration (LiveKit Cloud) and multistream fan-out

2) Data model & migrations
   - PostgreSQL schema that matches the spec
   - Event log (`session_state_events`) and sequencing
   - Redis usage for ephemeral state
   - Object storage layout for recordings/artifacts

3) API design (NestJS)
   - REST/HTTP + WebSocket events
   - Endpoints for scheduling, joining, roles/permissions, scene changes, chat/Q&A/polls, recording control, replay access
   - Request/response schemas
   - Idempotency and validation rules

4) Frontend app plan (Next.js)
   - Teacher UI, Student UI, Assistant UI (core screens)
   - Live session join flow, scene switching, chat/Q&A/polls, captions
   - Replay view with chapters/highlights/transcripts

5) Artifact pipeline
   - Stage-by-stage design: capture -> raw extraction -> alignment -> knowledge derivation
   - Jobs/queues, retry policy, idempotency, and failure tolerance
   - How artifacts link to the event log

6) Security & compliance
   - OAuth2/OIDC, JWTs, RBAC, session-scoped permissions
   - mTLS between services, secrets management, audit logs
   - FERPA/GDPR/COPPA considerations in storage and access

7) Deployment & ops
   - Local dev via docker-compose (Postgres, Redis, MinIO)
   - Environments, observability, logging, metrics
   - Scalability assumptions and tradeoffs

8) Acceptance criteria + test plan
   - Integration tests for key flows (start session, join, scene switch, Q&A, end session, artifact generation, replay)
   - Load testing targets for live sessions

9) Local Test Stack Plan (Managed LiveKit)
   - Provide a `docker-compose.yml` running Postgres, Redis, and MinIO locally.
   - Provide a `.env.local` template with:
     - `DATABASE_URL`, `REDIS_URL`, `S3_ENDPOINT`, `S3_ACCESS_KEY`, `S3_SECRET_KEY`
     - LiveKit Cloud: `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`
   - Describe how the app generates LiveKit access tokens server-side.
   - Include commands to:
     - Run migrations + seed data
     - Start API + worker locally
     - Run unit/integration/e2e tests
   - Note that media traffic goes to LiveKit Cloud; all other services are local.

Important constraints:
- The classroom must not break if async features fail; only critical sync failures should halt a class.
- Event log is the authoritative state for live and replay.
- Each feature must have a single owning service (per feature_backend_mapping).

If any spec detail is ambiguous, make a reasonable assumption and call it out explicitly.
