# teaching_first_database_schema.md
## Teaching-First Database Schema (Draft)

This file was split out from `multistream/docs/master_plan.md` solely to improve readability.

This document defines the persistent truth model for a teaching-first, multi-streaming platform. It is scoped to **authoritative truth** (PostgreSQL), with explicit separation for ephemeral data (Redis) and blob storage (Object Storage).

---

## 1. Multi-Tenant Foundation

### `tenants`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| name | text | Display name |
| status | text | `active`, `suspended` |
| created_at | timestamptz | |
| updated_at | timestamptz | |

### `tenant_domains`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| tenant_id | uuid | FK → `tenants.id` |
| domain | text | Unique domain |
| created_at | timestamptz | |

---

## 2. Identity & Roles

### `users`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| tenant_id | uuid | FK → `tenants.id` |
| email | citext | Unique per tenant |
| display_name | text | |
| status | text | `active`, `inactive` |
| created_at | timestamptz | |
| updated_at | timestamptz | |

### `roles`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| tenant_id | uuid | FK → `tenants.id` |
| name | text | `teacher`, `student`, `assistant`, `admin` |

### `user_roles`
| Column | Type | Notes |
| --- | --- | --- |
| user_id | uuid | FK → `users.id` |
| role_id | uuid | FK → `roles.id` |
| scope | text | `tenant`, `course`, `session` |
| scope_id | uuid | Nullable based on scope |

---

## 3. Courses, Lessons, Sessions

### `courses`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| tenant_id | uuid | FK → `tenants.id` |
| title | text | |
| status | text | `draft`, `published` |
| created_at | timestamptz | |

### `lessons`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| course_id | uuid | FK → `courses.id` |
| title | text | |
| order_index | int | |
| created_at | timestamptz | |

### `sessions`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| lesson_id | uuid | FK → `lessons.id` |
| status | text | `scheduled`, `live`, `ended`, `archived` |
| scheduled_at | timestamptz | |
| started_at | timestamptz | |
| ended_at | timestamptz | |
| created_at | timestamptz | |

### `session_participants`
| Column | Type | Notes |
| --- | --- | --- |
| session_id | uuid | FK → `sessions.id` |
| user_id | uuid | FK → `users.id` |
| role | text | `teacher`, `student`, `assistant` |
| join_time | timestamptz | |
| leave_time | timestamptz | |
| permissions | jsonb | Session-scoped overrides |

---

## 4. Event-Sourced Core

### `session_state_events`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| event_type | text | e.g., `session.started`, `scene.changed` |
| event_payload | jsonb | Immutable payload |
| sequence | bigint | Monotonic per session |
| idempotency_key | text | Dedupe key from write request |
| created_at | timestamptz | |

Constraints:
- Unique `(session_id, sequence)`
- Unique `(session_id, idempotency_key)`

---

## 5. Teaching Studio State

### `studio_scenes`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| name | text | |
| layout | jsonb | Layout definition |
| created_at | timestamptz | |

### `studio_assets`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| asset_type | text | `slide`, `whiteboard`, `media` |
| uri | text | Object storage pointer |
| created_at | timestamptz | |

---

## 6. Artifacts & Replays

### `recordings`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| status | text | `recording`, `finalized`, `failed` |
| media_uri | text | Object storage pointer |
| created_at | timestamptz | |

### `artifacts`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| artifact_type | text | `transcript`, `chapter`, `highlight`, `note` |
| payload | jsonb | Structured content |
| created_at | timestamptz | |

### `replays`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| status | text | `draft`, `published` |
| publish_at | timestamptz | |

---

## 7. Interaction & Engagement

### `chat_messages`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| user_id | uuid | FK → `users.id` |
| message | text | |
| created_at | timestamptz | |

### `questions`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| user_id | uuid | FK → `users.id` |
| prompt | text | |
| status | text | `open`, `answered` |
| created_at | timestamptz | |

### `polls`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| session_id | uuid | FK → `sessions.id` |
| prompt | text | |
| options | jsonb | |
| created_at | timestamptz | |

---

## 8. Integrations & Multistream Isolation

### `integrations`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| tenant_id | uuid | FK → `tenants.id` |
| provider | text | `youtube`, `twitch`, `vimeo`, etc. |
| status | text | `active`, `revoked` |
| created_at | timestamptz | |

### `multistream_destinations`
| Column | Type | Notes |
| --- | --- | --- |
| id | uuid | Primary key |
| integration_id | uuid | FK → `integrations.id` |
| destination_name | text | |
| stream_key_ref | text | Secret reference |
| created_at | timestamptz | |

### `session_destinations`
| Column | Type | Notes |
| --- | --- | --- |
| session_id | uuid | FK → `sessions.id` |
| destination_id | uuid | FK → `multistream_destinations.id` |
| status | text | `enabled`, `disabled`, `failed` |
| created_at | timestamptz | |

---

## 9. Data Plane Notes

- **Redis**: ephemeral presence, typing indicators, and live session counters.
- **Object Storage**: raw recordings, slides, whiteboards, generated media artifacts.
- **Search Index**: transcript and chapter search, replay discovery.

---

## 10. Correctness Requirements

- `session_state_events` is immutable and strictly ordered per session.
- No artifact is persisted without a corresponding session event trail.
- Multistream destinations are tenant-scoped and never shared across tenants.
- Replay publishing requires a finalized recording and at least one artifact.
