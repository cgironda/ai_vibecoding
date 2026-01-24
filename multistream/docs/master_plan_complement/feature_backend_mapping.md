# feature_backend_mapping.md
## Feature → Backend Mapping (Draft)

This file was split out from `multistream/docs/master_plan.md` solely to improve readability.

This document maps every user-facing feature to a single owning service, its source-of-truth data, sync vs async behavior, and failure mode expectations.

| Feature | Owning Service | Source of Truth | Sync/Async | Failure Behavior |
| --- | --- | --- | --- | --- |
| Scheduling | Classroom Orchestrator | `sessions` | Sync | Hard-fail start if scheduling invalid |
| Joining | Classroom Orchestrator | `session_participants` | Sync | Hard-fail join if identity invalid |
| Permissions | Identity & Roles | `user_roles`, `session_participants.permissions` | Sync | Hard-fail critical permissions |
| Scenes | Teaching Studio State | `studio_scenes` | Sync | Degrade: retain last good scene |
| Slides | Teaching Studio State | `studio_assets` | Sync | Degrade: fallback to default slide |
| Whiteboard | Teaching Studio State | `studio_assets` | Sync | Degrade: snapshot last saved state |
| Chat | Interaction Service | `chat_messages` | Async | Degrade: allow class to continue |
| Q&A | Interaction Service | `questions` | Async | Degrade: continue class |
| Polls | Interaction Service | `polls` | Async | Degrade: continue class |
| Captions | Artifact Generator | `artifacts` | Async | Degrade: live class continues |
| Notes | Artifact Generator | `artifacts` | Async | Degrade: live class continues |
| Recording | Media Plane | `recordings` | Sync | Hard-fail if recorder cannot arm |
| Replays | Replay & Knowledge UX | `replays` | Async | Degrade: replay delayed |
| Chapters | Artifact Generator | `artifacts` | Async | Degrade: replay missing chapters |
| Highlights | Artifact Generator | `artifacts` | Async | Degrade: replay missing highlights |
| Multistreaming | Media Plane / Fan-out | `session_destinations` | Async (Phase 2) | Degrade: class continues (Phase 2) |
| CTAs / QR Codes | Classroom Orchestrator | `session_state_events` | Sync | Degrade: fallback to static CTA |

---

## Notes

- **Single ownership** is mandatory: every feature is managed by one service only.
- **Source-of-truth** is either a relational table or the event log; caches must not diverge.
- **Failure behavior** follows teaching-first rules: if it impacts classroom continuity, it is critical; otherwise, it must degrade gracefully.
- **Multistreaming** to external destinations is Phase 2 and not required for MVP.
