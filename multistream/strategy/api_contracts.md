# api_contracts.md
## Teaching-First Multi-Streaming Platform — API Contracts (Draft)

### Authentication
POST /auth/login  
POST /auth/refresh  
POST /auth/logout  

---

### Classroom Orchestrator
POST /courses  
POST /lessons  
POST /lesson-runs  
POST /sessions/start  
POST /sessions/{id}/end  
GET  /sessions/{id}/state  

---

### Participation
POST /sessions/{id}/join  
POST /sessions/{id}/leave  
POST /sessions/{id}/permissions  

---

### Studio State
POST /sessions/{id}/scene  
POST /sessions/{id}/slides  
POST /sessions/{id}/layout  

---

### Interaction
POST /sessions/{id}/chat  
POST /sessions/{id}/questions  
POST /sessions/{id}/polls  

---

### Artifacts
GET /sessions/{id}/transcript  
GET /sessions/{id}/chapters  
GET /sessions/{id}/highlights  

---

### Replay
POST /sessions/{id}/publish  
GET  /replays/{id}  

---

### Multistream
POST /destinations  
POST /sessions/{id}/destinations  

---

### Webhooks
POST /webhooks  
GET  /webhooks/logs  
