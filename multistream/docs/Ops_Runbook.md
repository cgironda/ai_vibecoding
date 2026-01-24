# Ops Runbook - Teaching-First Multi-Streaming Platform

This runbook covers deployment checks and common incident playbooks.

## 1. Environments and Dependencies
- Web app: Next.js
- Artifact engine: FastAPI workers
- Data: Postgres, Redis, S3-compatible storage
- Media plane: LiveKit Cloud

## 2. Release Checklist
- Apply database migrations
- Verify env vars and secrets (DB, Redis, S3, LiveKit, webhook secret)
- Confirm LiveKit webhook endpoint is reachable
- Run smoke tests: schedule -> start -> join -> end
- Verify artifact job enqueue on room completion

## 3. Monitoring and Alerts
- Metrics: session start latency, join success rate, job queue depth
- Logs: structured JSON with requestId and sessionId
- Alerts: LiveKit webhook failures, DB write errors, worker backlog

## 4. Incident Playbooks

### Session Start Failure
Symptoms: start returns 5xx/403, no LiveKit room created
Immediate checks:
- RBAC logs for the actor
- LiveKit API availability and credentials
- DB write success for `session.started`
Mitigation:
- Retry start if idempotency key reused
- Disable start for the tenant if LiveKit outage

### LiveKit Outage
Symptoms: widespread join failures, webhook timeouts
Immediate checks:
- LiveKit status page and API error rates
- Token generation errors in web app logs
Mitigation:
- Block new starts, show status banner
- Queue webhook retries; do not drop events

### Webhook Failures
Symptoms: missing `participant_joined`/`room_finished` events
Immediate checks:
- Signature verification errors
- Network reachability of webhook endpoint
Mitigation:
- Replay webhook events if LiveKit allows redelivery
- Trigger manual artifact processing for affected sessions

### Artifact Pipeline Backlog
Symptoms: replay stays in "processing", queue depth grows
Immediate checks:
- Worker health and Redis connectivity
- Object storage access errors
Mitigation:
- Scale workers horizontally
- Pause non-critical jobs (highlights) to clear backlog

### Event Log Write Failure
Symptoms: event insert errors, sequence gaps
Immediate checks:
- DB availability and constraint violations
- Idempotency key collisions
Mitigation:
- Halt class start/join if event writes fail
- Repair sequence gaps before resuming

## 5. Backups and Retention
- Nightly database backups with 30-day retention
- Object storage lifecycle policies for recordings and artifacts
- GDPR/FERPA deletion requests processed within SLA

## 6. Escalation
- On-call engineer paged for Sev-1 incidents
- Product owner notified for feature degradation lasting > 30 minutes

