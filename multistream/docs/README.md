# Teaching-First Multi-Streaming Platform (Antigravity-X Hybrid)

This repo documents a teaching-first, event-sourced live classroom platform built on Next.js, LiveKit, and a Python artifact pipeline.

## Documents (source of truth)
Note: `multistream/docs/master_plan.md` is the original master plan; `multistream/docs/master_plan_complement/feature_backend_mapping.md`, `multistream/docs/master_plan_complement/teaching_first_artifact_pipeline.md`, and `multistream/docs/master_plan_complement/teaching_first_database_schema.md` were split out solely to improve readability.
- `multistream/docs/PRD.md`: product goals, scope, and non-functional requirements
- `multistream/docs/master_plan.md`: decisions and implementation checklist
- `multistream/docs/Implementation_Plan.md`: execution roadmap (not a source of truth)
- `multistream/docs/Dev_Ready_Spec.md`: dev-ready scope with module boundaries and constraints
- `multistream/docs/API_Event_Contracts.md`: human-readable API + event contracts
- `multistream/docs/openapi.yaml`: machine-readable API spec (OpenAPI)
- `multistream/docs/UX_Spec.md`: UX flows, screens, and acceptance criteria
- `multistream/docs/Ops_Runbook.md`: release checks and incident playbooks
- `multistream/docs/master_plan_complement/master_specification.md`: master teaching-first specification
- `multistream/docs/master_plan_complement/feature_backend_mapping.md`: feature ownership and failure modes
- `multistream/docs/master_plan_complement/teaching_first_database_schema.md`: authoritative schema
- `multistream/docs/master_plan_complement/teaching_first_artifact_pipeline.md`: artifact pipeline stages
- `multistream/docs/Phase2_Restream_Spec.md`: Phase 2 Restream-like multistreaming spec (future work)

## Suggested implementation order
1) Confirm scope and Phase 2 items
2) Lock schema + event contracts + idempotency rules
3) Build core session lifecycle APIs and event logging
4) Add studio state + interaction services
5) Build artifact pipeline + replay UX
6) Harden ops, monitoring, and tests

## Local setup (dev)
- `multistream/infra/docker-compose.yml` for Postgres, Redis, MinIO
- `.env.local` should include:
  - `DATABASE_URL`, `REDIS_URL`
  - `S3_ENDPOINT`, `S3_ACCESS_KEY`, `S3_SECRET_KEY`
  - `LIVEKIT_URL`, `LIVEKIT_API_KEY`, `LIVEKIT_API_SECRET`
  - `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`, `CLERK_SECRET_KEY`

## OpenAPI usage
`multistream/docs/openapi.yaml` is the canonical API spec for tooling (docs, clients, validators).

Examples (optional):
```bash
npx @redocly/cli lint multistream/docs/openapi.yaml
npx @openapitools/openapi-generator-cli generate \
  -i multistream/docs/openapi.yaml \
  -g typescript-fetch \
  -o ./generated/api-client
```

## Phase 2 (not MVP)
- External multistreaming destinations (YouTube/Twitch)

## Decisions (locked for MVP)
- Auth provider: Clerk.
- Worker queue: Celery (Redis backend).
- Unit tests: Vitest (Playwright for E2E).
- Early scaffolding: real local infra (no mocks).

## Notes
- API payloads use camelCase; database columns use snake_case.
- Idempotency is required for all write endpoints.
