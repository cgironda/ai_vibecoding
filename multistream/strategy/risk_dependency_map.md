# risk_dependency_map.md
## Teaching-First Platform — Risk & Dependency Map

### 1. Structural Risks

| Risk | Impact | Mitigation |
|------|--------|------------|
| Building Studio before Classroom logic | Fatal | Lock Orchestrator first |
| No event backbone | Fatal | Implement session_state_events early |
| Media coupled to UI | High | Enforce service boundaries |
| No artifact pipeline | High | Treat artifacts as core |
| Platform-first viewers | Medium | Require owned classroom viewer |

---

### 2. Technical Dependencies

| Feature | Depends On |
|--------|------------|
| Scene switching | Orchestrator, Studio State, Composer |
| Replays | Recorder, Artifact Generator |
| Chapters | Event log, Alignment engine |
| Engagement analytics | Event log, Aggregators |
| Multistream | Media Plane, Fan-out |

---

### 3. Kill-Switch Failures

If these fail → class must not start:
- Identity & Roles
- Classroom Orchestrator
- Media Plane

All others must degrade.

---

### 4. Validation Gates

| Gate | Must Prove |
|------|------------|
| G1 | Session lifecycle is correct |
| G2 | Permissions enforced |
| G3 | Events captured |
| G4 | Recording works |
| G5 | Replay searchable |
| G6 | Artifacts aligned |
