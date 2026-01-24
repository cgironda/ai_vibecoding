# Phase 2 Spec: Restream-like Multistreaming

> **Context**: This document specifies the "Phase 2" features required to achieve parity with Restream.io (broadcasting to external destinations like YouTube/Twitch). It is an addendum to the core "Teaching-First" documentation and does not modify the original MVP specs.

## 1. Feature Overview
**Goal**: Allow teachers to configure external RTMP destinations and broadcast their live session to them simultaneously.

**Key Capabilities**:
1.  **Destination Management**: Add/Connect YouTube, Twitch, or Custom RTMP accounts.
2.  **Stream Toggle**: Enable/Disable specific destinations per session.
3.  **Fan-out**: Server-side forwarding of the media stream to these destinations.

---

## 2. UX / UI Additions (Extends `UX_Spec.md`)

### 2.1 Dashboard > Destinations
**New Page**: `/dashboard/destinations`
*   **List View**: Shows connected accounts (e.g., "YouTube Channel: MyClass", "Twitch: Gaming101").
*   **Status Indicators**: "Connected", "Expired" (for OAuth tokens).
*   **"Add Destination" Action**:
    *   **Modal**: Two tabs.
        *   *Tab 1: Custom RTMP*: Inputs for "Server URL" and "Stream Key".
        *   *Tab 2: OAuth Services*: Buttons for "Connect with YouTube", "Connect with Twitch".

### 2.2 Dashboard > Create/Edit Session
**Section Addition**: "Multistreaming"
*   **Checkbox List**: "Select destinations for this session:"
    *   [ ] YouTube (MyClass)
    *   [ ] Custom RTMP (Backup)
*   **Default**: Defined by user preference (can be "All On" or "All Off").

---

## 3. API Specification (Extends `Dev_Ready_Spec.md`)

### 3.1 Destination Management
*   **GET /api/destinations**: List all configured destinations for the current tenant.
*   **POST /api/destinations**:
    *   Body: `{ type: 'rtmp' | 'youtube' | 'twitch', name: string, streamKey?: string, url?: string, oauthToken?: string }`
    *   Returns: `Destination` object.
*   **DELETE /api/destinations/:id**: Remove a destination.
*   **POST /api/destinations/:id/test**: (Optional) momentarily verifies the RTMP connection.

### 3.2 Session Control
*   **POST /api/sessions/:id/destinations**:
    *   Body: `{ destinationIds: string[] }` (Enable specific destinations for this session).
*   **GET /api/sessions/:id/destinations**: List active destinations for a running session.

---

## 4. Backend Architecture (Extends `feature_backend_mapping.md`)

### 4.1 Service Ownership
*   **Owning Service**: `Media Plane` (LiveKit + Egress Service).
*   **Source of Truth**: `session_destinations` table (already exists in schema).

### 4.2 Data Flow (The "Fan-out")
1.  **Trigger**: When `session.started` event occurs (or manually triggered via API).
2.  **Action**: The backend looks up enabled `session_destinations` for that session.
3.  **Execution**:
    *   For each destination, the backend calls the **LiveKit Egress API**.
    *   Command: `StartRTMP({ rtmp_urls: [url1, url2] })`.
    *   *Note: LiveKit Egress handles the transcoding and pushing to RTMP.*
4.  **Monitoring**:
    *   Listen for `EgressStarted` and `EgressEnded` webhooks from LiveKit.
    *   Update `session_destinations.status` to 'live' or 'failed'.

---

## 5. Database Requirements (References `teaching_first_database_schema.md`)
The schema already supports this in Section 8 "Integrations & Multistream Isolation", but this spec enforces its usage:
*   `integrations`: Stores valid OAuth tokens for platforms.
*   `multistream_destinations`: Stores the resolved RTMP URL and Stream Key.
*   `session_destinations`: Join table activating a destination for a specific session.
