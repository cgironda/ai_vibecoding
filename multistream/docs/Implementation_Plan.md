# Implementation Plan - Teaching-First Platform Roadmap

## Role of this document (Captain)
This plan is the execution roadmap. It does not replace the canonical specs; it points to them as "Manuals" for authoritative details.

## Manuals (source of truth)
- `multistream/docs/master_plan.md` (scope, architecture, milestones)
- `multistream/docs/Dev_Ready_Spec.md` (modules, contracts, constraints)
- `multistream/docs/UX_Spec.md` (flows and acceptance criteria)
- `multistream/docs/API_Event_Contracts.md` (API + event payloads)
- `multistream/docs/Ops_Runbook.md` (ops and incident response)
- `multistream/docs/master_plan_complement/*` (schema, pipeline, feature mapping, master specification)

If this plan conflicts with a Manual, the Manual wins.

## Scope (MVP)
- In scope: session lifecycle, studio state, interaction services, artifact pipeline, replay UX, ops hardening.
- Out of scope (Phase 2): external multistream destinations (YouTube/Twitch fan-out). See `multistream/docs/Phase2_Restream_Spec.md` for future work.

## Decisions (locked for MVP)
- Auth provider: Clerk.
- Worker queue: Celery with Redis backend.
- Unit test runner: Vitest.
- Early scaffolding: real local infra (Postgres, Redis, MinIO) and LiveKit credentials; no mocks.

## Phase 2 readiness (non-executable)
These notes are informational only; they do not change scope or commit to work.
- Keep streaming integrations behind a service boundary.
- Avoid hard-coded assumptions about a single destination.
- Capture Phase 2 requirements as explicit TODOs (docs or code).

## Preconditions and checks
- Confirm repo state before scaffolding; do not assume an empty repository.
- Confirm local infra and environment variables per `multistream/docs/master_plan.md`.
- Confirm LiveKit managed credentials are available.

## Milestones and execution
### M1: Repo + infra + schema + auth baseline
- Ensure monorepo layout and tooling align with `multistream/docs/Dev_Ready_Spec.md`.
- Set up infra (Postgres, Redis, MinIO) and `.env.local` template per `multistream/docs/master_plan.md`.
- Implement authoritative schema and constraints from `multistream/docs/master_plan_complement/teaching_first_database_schema.md`.
- Add shared types/validators and base error format from `multistream/docs/Dev_Ready_Spec.md`.
- Establish linting, typecheck, and test skeletons.

### M2: Session lifecycle + LiveKit integration
- Implement schedule/start/join/end flows with idempotency and event log writes first.
- Generate LiveKit tokens server-side and validate webhooks.
- Broadcast state changes via WebSocket/LiveKit data channels after event logging.
- Verify against `multistream/docs/API_Event_Contracts.md` and acceptance criteria.

### M3: Teaching studio + interaction services
- Build studio scenes/assets CRUD with event-sourced updates.
- Implement chat, Q&A, and polls with persistence and graceful degradation.
- Ensure client reconciliation from the event log on reconnect.

### M4: Artifact pipeline
- Implement the FastAPI artifact engine and Redis-backed queues.
- Build extraction, alignment, and derivation tasks with idempotent upserts.
- Trigger pipeline from LiveKit `room_finished` and validate replay readiness states.

### M5: Replay UX + ops hardening
- Implement replay APIs and UI (timeline, chapters, transcript, highlights).
- Add observability, runbooks, and failure-mode tests.
- Ensure definition-of-done in `multistream/docs/master_plan.md` is met.

## Verification plan
- Unit tests: RBAC, idempotency, schema constraints.
- Integration tests: session lifecycle, studio updates, LiveKit token generation.
- Worker tests: artifact pipeline retries and idempotent upserts.
- E2E (Playwright): teacher starts session, student joins, interactions work.
- Manual checks: replay loads with recordings; failure modes degrade gracefully.

## Phase 2 note
External multistreaming is explicitly Phase 2 and not part of this roadmap. Track it separately in `multistream/docs/Phase2_Restream_Spec.md`.
