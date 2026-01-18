# plan_dev.md
## System Specification for a Teaching-First Multi-Streaming Platform

This document defines the **system specification** (not the execution timeline) for a teaching-first, multi-streaming platform. These steps describe **what must exist**, **how it must be structured**, and **what correctness means**.

---

## 1. Strategic Reframing

**Goal:** Define the product as a classroom system that happens to livestream.

### Tasks
- Establish that this is a **teaching-first** platform, not a webinar tool.
- Identify core pedagogical needs: roles, classroom logic, artifacts, replays, engagement.
- Define success metrics based on learning outcomes, not viewer counts.
- Document the product principle:
  > Classroom-first → Media-second → Distribution-last.

---

## 2. Teaching-First Architecture

**Goal:** Define system layers and boundaries.

### Tasks
- Define the major layers:
  - Client Layer (Teacher, Student, Assistant, Public)
  - API Gateway / BFF
  - Control Plane (authoritative logic)
  - Media Plane (real-time systems)
  - Data Plane (persistence and search)
- Define responsibilities of each layer.
- Define isolation rules between layers.
- Document which layers are allowed to fail without stopping a class.

---

## 3. Service Decomposition

**Goal:** Break the system into purpose-driven services.

### Tasks
- Define each service and its single responsibility:
  1. Identity & Roles
  2. Classroom Orchestrator
  3. Teaching Studio State
  4. Media Plane (SFU, Encoder, Recorder)
  5. Artifact Generator
  6. Replay & Knowledge UX
  7. Interaction Service
  8. Analytics & Learning Intelligence
  9. Multistream Fan-out
  10. Integration Gateway
- Assign ownership boundaries.
- Define what each service is NOT responsible for.
- Define service-to-service dependencies.

---

## 4. Component Diagram & Interaction Flows

**Goal:** Specify how services communicate.

### Tasks
- Define synchronous vs asynchronous interactions.
- Define real-time vs deferred processing paths.
- Define which failures must halt a class.
- Define which failures degrade gracefully.
- Model:
  - Session start
  - Student join
  - Scene switching
  - Q&A handling
  - Replay generation

---

## 5. Teaching-First Database Schema

**Goal:** Define the persistent truth model.

### Tasks
- Define multi-tenant data structures.
- Define role-based access models.
- Define session lifecycle tables.
- Define event-sourced core (`session_state_events`).
- Define artifact storage models.
- Define replay and analytics tables.
- Define integration and multistream isolation tables.

---

## 6. Feature → Backend Mapping

**Goal:** Eliminate ambiguity about feature ownership.

### Tasks
For each user-facing feature:
- Assign a single owning service.
- Define source-of-truth tables.
- Define sync vs async behavior.
- Define failure behavior.
- Ensure no feature spans services without explicit contracts.

Features include:
- Scheduling
- Joining
- Permissions
- Scenes
- Slides
- Whiteboard
- Chat
- Q&A
- Polls
- Captions
- Notes
- Recording
- Replays
- Chapters
- Highlights
- Multistreaming
- CTAs / QR codes

---

## 7. Teaching-First Artifact Pipeline

**Goal:** Convert video into learning.

### Tasks
- Define the immutable event taxonomy.
- Define raw artifact extractors.
- Define temporal alignment processes.
- Define derived knowledge generation.
- Define replay intelligence requirements.
- Define near-real-time vs post-processing stages.
- Define failure tolerance rules.

Pipeline stages:
1. Live Event Capture
2. Raw Extraction
3. Temporal Alignment
4. Knowledge Derivation

---

## Definition

These seven steps collectively define the **system specification**.

They answer:
- What must exist
- How it must be structured
- Who owns what
- What correctness means

They do NOT define:
- Timeline
- Staffing
- Budget
- Milestones
- Execution order

Those belong to the **implementation plan**, which is a separate document.

---
