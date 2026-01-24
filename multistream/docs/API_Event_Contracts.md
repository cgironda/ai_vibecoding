# API and Event Contracts - Teaching-First Multi-Streaming Platform

This document defines the canonical request/response shapes for core APIs and the event envelope for `session_state_events`.

## 1. Conventions
- Base path: `/api`
- Format: JSON request/response
- Timestamps: ISO-8601 UTC (e.g., `2025-01-30T12:34:56Z`)
- IDs: UUID v4
- Idempotency: required on all POST/PATCH/DELETE. Prefer `Idempotency-Key` header; `idempotencyKey` in the body is accepted as a fallback.
- API payloads use camelCase; database columns use snake_case.
- Error format:
  ```json
  {
    "code": "string",
    "message": "string",
    "details": {},
    "requestId": "uuid"
  }
  ```

## 2. Authentication
- User-facing endpoints use Clerk (session cookie or `Authorization: Bearer <token>`).
- Internal service endpoints require a shared secret or mTLS and must be private-network only.

## 3. Common Objects
- Session:
  ```json
  {
    "id": "uuid",
    "lessonId": "uuid",
    "status": "scheduled|live|ended",
    "scheduledStart": "timestamp",
    "scheduledEnd": "timestamp"
  }
  ```
- Participant:
  ```json
  { "userId": "uuid", "role": "teacher|student|assistant" }
  ```
- Scene:
  ```json
  { "id": "uuid", "sessionId": "uuid", "name": "string", "layout": {} }
  ```

## 4. Session Endpoints
- `POST /api/sessions`
  - Request:
    ```json
    {
      "lessonId": "uuid",
      "scheduledStart": "timestamp",
      "scheduledEnd": "timestamp",
      "idempotencyKey": "string"
    }
    ```
  - Response: `{ "session": { ... } }`
- `POST /api/sessions/start`
  - Request: `{ "sessionId": "uuid", "idempotencyKey": "string" }`
  - Response: `{ "session": { ... }, "livekitToken": "string" }`
- `POST /api/sessions/join`
  - Request: `{ "sessionId": "uuid", "idempotencyKey": "string" }`
  - Response: `{ "session": { ... }, "participant": { ... }, "livekitToken": "string" }`
- `POST /api/sessions/end`
  - Request: `{ "sessionId": "uuid", "idempotencyKey": "string" }`
  - Response: `{ "session": { ... } }`

## 5. Studio Endpoints
- `POST /api/studio/scenes`
  - Request: `{ "sessionId": "uuid", "name": "string", "layout": {}, "idempotencyKey": "string" }`
  - Response: `{ "scene": { ... } }`
- `PATCH /api/studio/scenes/:sceneId`
  - Request: `{ "layout": {}, "idempotencyKey": "string" }`
  - Response: `{ "scene": { ... } }`
- `POST /api/studio/assets`
  - Request: `{ "sessionId": "uuid", "type": "slide|whiteboard|media", "idempotencyKey": "string" }`
  - Response: `{ "assetId": "uuid", "contentUrl": "string" }`

## 6. Interaction Endpoints
- `POST /api/chat`
  - Request: `{ "sessionId": "uuid", "content": "string", "idempotencyKey": "string" }`
  - Response: `{ "messageId": "uuid", "createdAt": "timestamp" }`
- `POST /api/questions`
  - Request: `{ "sessionId": "uuid", "content": "string", "idempotencyKey": "string" }`
  - Response: `{ "questionId": "uuid", "createdAt": "timestamp" }`
- `POST /api/polls`
  - Request: `{ "sessionId": "uuid", "prompt": "string", "options": ["string"], "idempotencyKey": "string" }`
  - Response: `{ "pollId": "uuid", "createdAt": "timestamp" }`

## 7. Replay and Artifact Endpoints
- `GET /api/replays/:sessionId`
  - Response:
    ```json
    {
      "sessionId": "uuid",
      "recordingUrl": "string",
      "transcriptUrl": "string",
      "chapters": [],
      "highlights": []
    }
    ```
- `POST /api/artifacts/process` (internal)
  - Request: `{ "sessionId": "uuid", "recordingUrl": "string", "idempotencyKey": "string" }`
  - Response: `{ "status": "accepted" }`

## 8. Webhooks
- `POST /api/webhooks/livekit`
  - Verifies LiveKit signature header.
  - Consumes `room_finished`, `participant_joined`, `participant_left`.
  - Response: `{ "status": "ok" }`

## 9. Event Envelope (`session_state_events`)
Each event is immutable and strictly ordered per session.

```json
{
  "id": "uuid",
  "sessionId": "uuid",
  "sequence": 123,
  "eventType": "string",
  "eventPayload": {},
  "createdAt": "timestamp",
  "idempotencyKey": "string"
}
```

## 10. Event Types and Required Payload Fields
| Event Type | Required Payload Fields |
| --- | --- |
| `session.started` | `startedAt`, `actorUserId` |
| `session.ended` | `endedAt`, `actorUserId` |
| `participant.joined` | `userId`, `role`, `joinedAt` |
| `participant.left` | `userId`, `leftAt` |
| `scene.changed` | `sceneId`, `layout` |
| `slide.changed` | `sceneId`, `assetId`, `pageIndex` |
| `whiteboard.updated` | `sceneId`, `assetId`, `checksum` |
| `chat.message` | `messageId`, `userId`, `content` |
| `question.asked` | `questionId`, `userId`, `content` |
| `question.answered` | `questionId`, `userId`, `content` |
| `poll.started` | `pollId`, `prompt`, `options` |
| `poll.ended` | `pollId`, `results` |
| `recording.started` | `recordingId` |
| `recording.finalized` | `recordingId`, `mediaUrl` |
