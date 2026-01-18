# teaching_first_artifact_pipeline.md
## Teaching-First Artifact Pipeline (Draft)

This pipeline converts live classroom activity into learning artifacts. It is **event-driven** and **artifact-centric**, built on immutable session events.

---

## 1. Event Taxonomy (Immutable)

Core event types captured in `session_state_events`:

- `session.started`, `session.ended`
- `participant.joined`, `participant.left`
- `scene.changed`, `slide.changed`
- `chat.message`, `question.asked`, `question.answered`
- `poll.started`, `poll.ended`
- `whiteboard.updated`
- `recording.started`, `recording.finalized`

---

## 2. Pipeline Stages

### Stage 1 — Live Event Capture (Near Real-Time)
- Capture authoritative events from the Classroom Orchestrator and Interaction Service.
- Persist to `session_state_events` with monotonic sequencing.
- Broadcast to subscribers for live UI updates.

### Stage 2 — Raw Extraction (Near Real-Time)
- Generate transcript segments (ASR) from audio.
- Capture slide and whiteboard snapshots at scene transitions.
- Emit raw artifacts to object storage with timecodes.

### Stage 3 — Temporal Alignment (Post-Processing + Streaming Updates)
- Align transcript, slide changes, and interaction events.
- Normalize timestamps across media and event log.
- Produce aligned artifact bundles for replay.

### Stage 4 — Knowledge Derivation (Post-Processing)
- Generate chapters from event density + transcript topic shifts.
- Derive highlights from Q&A, poll spikes, and teacher emphasis.
- Build searchable index for replay UX.

---

## 3. Near-Real-Time vs Post-Processing

| Output | Timing | Consumer |
| --- | --- | --- |
| Live captions | Near-real-time | Student UI |
| Transcript draft | Near-real-time | Teacher UI |
| Chapters | Post-processing | Replay UX |
| Highlights | Post-processing | Replay UX |
| Notes summary | Post-processing | Replay UX |

---

## 4. Failure Tolerance Rules

- **Event capture failure** → class must halt (authoritative log).
- **Transcript failure** → class continues; replay lacks captions.
- **Alignment failure** → class continues; replay lacks chapters.
- **Knowledge derivation failure** → class continues; replay without highlights.

---

## 5. Correctness Requirements

- Every artifact must link to a `session_state_events` trace.
- Alignment must be reproducible from the event log + raw media.
- Post-processing is idempotent and safe to rerun.
