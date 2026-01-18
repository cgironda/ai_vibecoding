# security_compliance.md
## Teaching-First Multi-Streaming Platform — Security & Compliance Layer

This document defines the security, privacy, and compliance foundations for a classroom-first platform.

---

## 1. Threat Model

### Actors
- Students
- Teachers
- Assistants
- Admins
- External Integrations
- Malicious actors

### Assets
- Personal data (PII)
- Classroom recordings
- Transcripts
- Student interactions
- Authentication credentials
- Destination stream keys

---

## 2. Security Architecture

### Identity & Access
- OAuth2 / OpenID Connect
- Short-lived JWTs
- Role-based access control (RBAC)
- Session-scoped permissions

### Secrets Management
- Encrypted at rest (KMS)
- Rotated regularly
- Never exposed to clients

### Network Security
- mTLS between services
- Private VPCs
- Zero-trust principles

---

## 3. Data Protection

### At Rest
- AES-256 encryption
- Separate encryption domains per tenant

### In Transit
- TLS 1.3 everywhere
- HSTS enforced

### Backups
- Encrypted
- Region-separated
- Regular restore tests

---

## 4. Privacy by Design

- Data minimization
- Purpose limitation
- Role-scoped access
- Student data isolation

---

## 5. Compliance Targets

### FERPA (US)
- Student educational record protection
- Access controls
- Audit logs

### GDPR (EU)
- Right to access
- Right to erasure
- Data portability
- Consent tracking

### COPPA (if minors)
- Parental consent
- Restricted data processing

---

## 6. Auditability

- Immutable event logs
- Access logs
- Permission change history
- Replay access logs

---

## 7. Content Safety

- Moderation tools
- Chat filtering
- Abuse detection
- Kill switches

---

## 8. Incident Response

- Detection pipelines
- Escalation policy
- Customer notification
- Forensic logging

---

## 9. Classroom-Specific Controls

- Recording consent
- Participant visibility controls
- Screen sharing restrictions
- Exam mode

---

## 10. Security Is a Feature

If security is bolted on later, this platform will fail in education markets.

