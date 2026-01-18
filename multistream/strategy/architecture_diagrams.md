# architecture_diagrams.md
## Teaching-First Multi-Streaming Platform — Architecture Diagrams (Textual)

### 1. High-Level System Diagram

Client Apps
  ├── Teacher UI
  ├── Student UI
  ├── Assistant UI
  └── Public Viewer
        ↓
API Gateway / BFF
        ↓
Control Plane
  ├── Identity & Roles
  ├── Classroom Orchestrator
  ├── Teaching Studio State
  ├── Interaction Service
  ├── Artifact Generator
  ├── Analytics & Learning Intelligence
  └── Integration Gateway
        ↓
Media Plane
  ├── WebRTC SFU
  ├── Composer / Encoder
  ├── Recorder
  └── Multistream Fan-out
        ↓
Data Plane
  ├── PostgreSQL (truth)
  ├── Redis (ephemeral)
  ├── Object Storage
  └── Search Index

---

### 2. Failure Isolation Diagram

If Multistream fails → Classroom continues  
If Analytics fails → Classroom continues  
If Artifact Generator fails → Classroom continues  
If Identity fails → Classroom halts  
If Orchestrator fails → Classroom halts  
If Media Plane fails → Classroom halts  

---

### 3. Event Flow Diagram (Simplified)

User Action → API Gateway → Owning Service → session_state_events → Subscribers
                                                   ↓
                                          Artifact Pipeline
