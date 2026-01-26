# UI/UX Design Plan: Multistream & Teaching Studio
**Objective**: Create a premium, "Restream-like" browser-based streaming studio that integrates specialized teaching tools.
**Target Audience**: Educators, Code Streamers, and Workshop Hosts.
**Location**: `apps/web` (Frontend logic).

---

## 1. Visual Identity & Aesthetics
The design will prioritize a "dark mode first" approach, similar to high-end production software (Restream, DaVinci Resolve, OBS).

*   **Color Palette**:
    *   **Backgrounds**: Deep Gunmetal (`#121212`, `#1E1E1E`) for main areas; slightly lighter (`#2C2C2C`) for panels/cards.
    *   **Accents**: Electric Blue or Blurple (similar to Restream/Discord) for primary actions (Go Live, Toggles).
    *   **Status**:
        *   🔴 Live/Recording: Bright Red (`#EF4444`).
        *   🟢 Healthy/Active: Neon Green (`#22C55E`).
        *   🟡 Warning/Unstable: Amber (`#F59E0B`).
*   **Typography**:
    *   **Font**: `Inter` or `Plus Jakarta Sans` for clean, high-legibility UI text.
    *   **Hierarchy**: Bold headers for sections, medium for controls, regular for content.
*   **UX Feel**:
    *   Glassmorphism (subtle): On overlays and floating panels.
    *   Micro-interactions: Hover effects on source thumbnails, smooth transitions between scenes.
    *   Responsive: Fluid layout that adapts to window resizing (crucial for single-monitor streamers).

---

## 2. Application Map

**Routes Overview**
*   `/` → Marketing landing (Restream-style hero + CTA + studio preview).
*   `/login` → Authentication entry (public).
*   `/onboarding/connect` → Destination connect step (authenticated or provisional session).
*   `/onboarding/permissions` → Device permission gate (authenticated).
*   `/dashboard` → Control Center (authenticated).
*   `/studio` → Teaching Studio cockpit (authenticated).

### A. Landing Page (Marketing)
Public-facing marketing page (route: `/`). Mirrors a Restream-style layout: hero headline, CTA row, product preview strip, and footer links. No dashboard or studio widgets here.

**Layout Structure**:
1.  **Top Navigation**
    *   Logo + primary nav (Products, Solutions, Use cases, Pricing, Contact).
    *   CTAs: Log in, Get started.
2.  **Hero**
    *   Big headline + supporting line.
    *   Primary CTA + secondary CTA (Google sign-in).
3.  **Studio Preview Block**
    *   Single showcase panel of the studio UI (non-interactive).
4.  **Footer Columns**
    *   Products, Community, Resources, Company.

### B. Dashboard (The "Control Center")
Authenticated control center (route: `/dashboard`). Focuses on configuration and analytics.

**Layout Structure**:
1.  **Sidebar (Left Collapsible)**
    *   Navigation: Home, Destinations, Recordings, Analytics, Billing/Team.
    *   "Enter Studio" (Prominent CTA Button).
2.  **Main Content Area (Center)**
    *   **Destinations Grid**: Cards showing connected accounts (YouTube, Twitch, LinkedIn).
        *   *Action*: Toggle switches (On/Off).
        *   *Action*: "Add Destination" (+ button).
    *   **Stream Setup**: Title, Description, Game/Category input fields (syncs to all).
    *   **Recent Streams**: List of past broadcasts with download/delete options.
3.  **Right Panel (Optional/Collapsible)**
    *   System Health (Internet check).
    *   News/Updates.

### C. Teaching Studio (The "Cockpit")
The core experience. A browser-based OBS alternative.

**Layout Structure**: Three-Column Layout (Left Control, Center Stage, Right Engagement).

#### 1. Left Sidebar: Production & Lesson Control
*   **Top Tabs**: **Scenes** | **Lesson Plan**
    *   **Scenes Tab**:
        *   Grid of scene thumbnails (cam only, screenshare, split view).
        *   "Add Scene" (+ button).
        *   *Teaching Specific*: "Whiteboard Scene" preset.
    *   **Lesson Plan Tab (Teaching Feature)**:
        *   A sequential list of items to cover (e.g., "1. Intro", "2. Code Layout", "3. Q&A").
        *   Clicking an item can auto-trigger a specific Scene or Overlay.
        *   Progress bar tracking lesson completion.
*   **Sources Panel (Bottom)**:
        *   List of active layers in selected scene (Webcam, Mic, Screen, Image, Widget).
        *   Toggles: Eye (Visibility), Lock (Position).
*   **Audio Mixer (Sticky Footer Panel)**:
        *   Per-source volume sliders (Mic, Desktop, Guests, Media).
        *   Toggles: Mute, Solo, Monitor.
        *   Filters: Noise Suppression, Echo Cancellation, Compressor (basic presets).

#### 2. Center Stage: The Feed
*   **Preview Canvas**: Large 16:9 area showing the final program output.
    *   **Manipulators**: Drag-and-drop resizing of webcam bubbles/screens directly on canvas.
*   **Toolbar (Floating Bottom Center)**:
    *   Mic (Mute/Unmute) - with VU Meter.
    *   Camera (On/Off).
    *   Share Screen.
    *   **Whiteboard Toggle**: Immediately overlays drawing tools on current view.
    *   Invite Guests (Copy Link).
    *   Settings (Cog).
*   **"GO LIVE" Header**:
    *   Big Red Button (Top Right of Center area).
    *   Timer (00:00:00).
    *   Viewer Count (Total).

#### 3. Right Sidebar: Engagement & Chat
*   **Tabs**: **Chat** | **Q&A** | **Captions**
    *   **Unified Chat**: Aggregates messages from all streaming platforms.
        *   *Action*: Click message to "Star" (show on screen overlay).
    *   **Q&A (Teaching Feature)**:
        *   Separate queue for questions.
        *   Students can "Upvote" questions (if using a companion student link).
        *   Teacher can mark "Answered" or "Show on Stream".
    *   **Captions/Graphics**:
        *   Quick toggles for "Lower Thirds" (Name tags).
        *   Ticker/Scrolling text setup.
*   **Moderation Tools (Teaching Safety)**:
    *   Quick actions: Hide message, Ban user, Timeout, Report.
    *   Auto-mod: Profanity filter + link blocking (toggle).
    *   Safety banner when moderation actions are active.


---

## 3. Critical User Flows
Step-by-step UI states for core actions to ensure smoothness.

### A. Onboarding & Setup
1.  **Connect Destination**:
    *   *Screen 1*: "Add Destination" Modal -> Grid of logos (YouTube, Twitch, etc.).
    *   *Screen 2*: OAuth Popup window.
    *   *Screen 3*: Success Toast ("YouTube Connected") + Card appears in Dashboard.
2.  **Permission Gate (Pre-Studio)**:
    *   *Screen*: "Let's check your gear" (Browser prompt intercept).
    *   *UI*: Mic/Cam selection dropdowns + VU meter + Camera Preview.
    *   *Action*: "Enter Studio" enabled only after permissions granted.

### B. The "Go Live" Sequence
1.  **Idle**: "Go Live" button is Blue. Status: "Ready".
2.  **Initiating**: User clicks "Go Live". Button spins/pulses. Status: "Connecting...".
3.  **Live**: Button turns Red "End Event". Timer starts. Tally light on Preview border.
4.  **Distribution**: Icons for YouTube/Twitch in top bar light up (Grey -> Color).

### C. Teaching Actions
1.  **Trigger Whiteboard**:
    *   *Action*: Click "Whiteboard" icon in toolbar.
    *   *Transition*: Tool palette slides in from left. Canvas becomes slightly dimmed/overlay ready.
2.  **Next Lesson Step**:
    *   *Action*: Click "2. Code Layout" in Lesson Plan.
    *   *Transition*: Scene auto-switches to "Screen Share + PIP Camera" scene defined in settings.

---

## 4. Recovery & Error Flows
Explicit recovery paths for common failure modes.

### A. Device Failures
1.  **Permission Denied (Mic/Cam)**:
    *   *State*: Permission banner appears with "Open Settings" CTA.
    *   *Recovery*: Inline steps + retry button after permission change.
2.  **Device Unplugged / Lost**:
    *   *State*: Source tile shows placeholder + "Reconnecting..." toast.
    *   *Recovery*: Auto-reconnect attempts (3x), then prompt to select a new device.

### B. Network & Destination Failures
1.  **Network Drop**:
    *   *State*: "Reconnecting..." overlay + degraded mode indicator.
    *   *Recovery*: Auto resume; if >30s, show "End Stream" vs "Keep Trying".
2.  **Destination Auth Expired**:
    *   *State*: Destination card turns amber + "Reconnect" CTA.
    *   *Recovery*: OAuth re-auth modal without leaving studio.

### C. Recording Failures
1.  **Server Recording Failed**:
    *   *State*: Banner "Server recording lost".
    *   *Recovery*: Auto-enable local `MediaRecorder` with storage warning.

---

## 5. States & Errors Matrix
Defining how the UI reacts to different conditions.

| Component | State: Idle | State: Active/Live | State: Error/Disconnected |
| :--- | :--- | :--- | :--- |
| **Microphone** | Icon: White | Icon: Green Pulse (VU) | Icon: Red Slash + Toast "Mic Lost" |
| **Camera** | View: Feed | View: Feed with Red Border | View: Static Noise/Placeholder + "Reconnecting..." |
| **Destinations**| Toggle: Off | Toggle: On (Green Dot) | Toggle: Amber Warning + "Retry" Button |
| **Chat** | "Connecting..." | Live Message Stream | "Chat Disconnected - Retrying..." (Greyed out) |
| **Network** | Signal: 5 Bars | Signal: Dynamic (Green/Yellow) | Signal: Red Bar + "Unstable Connection" Banner |
| **Audio Mixer** | Sliders Disabled | Sliders Active + VU | Red Clip Warning + "Reduce Gain" |

---

## 6. Accessibility & Inclusivity
*   **Contrast**: All text must pass WCAG AA (4.5:1). Dark mode backgrounds will potentially use lighter grays (`#B3B3B3`) for text rather than pure white to reduce eye strain, but high contrast for alerts.
*   **Keyboard Navigation**:
    *   `Space`: Mute/Unmute.
    *   `1-9`: Switch Scenes.
    *   `Tab`: Logical focus order (Sidebar -> Stage -> Right Panel).
*   **Screen Readers**: Aria labels on all icon-only buttons (e.g., `aria-label="Toggle Whiteboard"`).
*   **Captions**: Reserved space at the bottom of the video canvas for future STT (Speech-to-Text) output.

---

## 7. Technical Assumptions & Protocols
*   **Ingest Protocol**: WHIP (WebRTC-HTTP Ingestion Protocol) for lowest latency (<200ms) is primary. Fallback to RTMP if needed for specific destinations (handled by backend).
*   **Recording**: Client-side recording via `MediaRecorder API` as backup; Server-side recording preferred.
*   **Latency Targets**:
    *   Studio Preview: ~50ms (Real-time).
    *   Viewer (Ultra-low latency): ~2-5s.
    *   Viewer (Standard): ~15-30s.

---

## 8. Studio Health & Telemetry
Surface real-time performance metrics and thresholds.
*   **Metrics**:
    *   Bitrate (kbps), FPS, dropped frames, encoder load, CPU/GPU, memory.
    *   Per-destination health indicators (green/yellow/red).
*   **Thresholds**:
    *   **Bitrate**: <70% target for 10s => warn; <50% for 30s => critical.
    *   **Dropped Frames**: >2% for 10s => warn; >5% for 10s => critical.
    *   **Encoder Load**: >85% for 20s => warn; >95% for 10s => critical.
*   **Actions**:
    *   Suggest lowering resolution/bitrate, disabling effects, or switching scene.
    *   Provide one-click "Reduce Load" preset (drops FPS, turns off blur).

---

## 9. Design Tokens (Style Guide)
Keeping the UI consistent.
*   **Spacing**: Base unit 4px.
    *   `xs`: 4px, `sm`: 8px, `md`: 16px, `lg`: 24px, `xl`: 32px.
*   **Typography Scale**:
    *   `display`: 28px/36px, `h1`: 22px/30px, `h2`: 18px/26px, `body`: 14px/20px, `caption`: 12px/16px.
*   **Radius**:
    *   `sm`: 4px (Buttons).
    *   `md`: 8px (Cards, Video inputs).
    *   `lg`: 16px (Modals).
*   **Color Ramp**:
    *   `bg-900`: `#121212`, `bg-800`: `#1E1E1E`, `bg-700`: `#2C2C2C`, `bg-600`: `#3A3A3A`.
    *   `text-100`: `#F5F5F5`, `text-200`: `#D9D9D9`, `text-300`: `#B3B3B3`.
*   **Elevation (Shadows)**:
    *   `1`: `0 1px 2px rgba(0,0,0,0.5)` (Cards).
    *   `2`: `0 4px 12px rgba(0,0,0,0.5)` (Dropdowns/Modals).
    *   `3`: `0 12px 24px rgba(0,0,0,0.6)` (Floating Toolbar).
*   **Motion**:
    *   `quick`: 150ms ease-out (Hover states).
    *   `smooth`: 300ms ease-in-out (Panel slides, Scene transitions).
*   **States**:
    *   **Focus**: 2px outline `#60A5FA` + subtle glow for keyboard navigation.
    *   **Disabled**: 40% opacity + cursor `not-allowed`.

---

## 10. QA & Testing Checklist
*   [ ] **Responsiveness**: Test studio layout at 1080p, 720p, and split-screen (half width).
*   [ ] **Browser Compatibility**: Chrome, Firefox, Safari (check WebRTC quirks).
*   [ ] **Drag & Drop**: Ensure video sources don't overlap strangely or get "stuck".
*   [ ] **Load Testing**: Chat panel performance with rapid message influx.
*   [ ] **Audio Routing**: Verify muting local mic doesn't mute system audio if sharing screen.

---

## 11. Implementation Priority (Revised)
1.  **Project Init**: Next.js + Tailwind + Design Tokens setup.
2.  **Auth & Onboarding**: Simplified Login -> Destination Connect -> Permission Gate.
3.  **Studio Skeleton**: Resizable 3-pane Grid.
4.  **Core Media**: `getUserMedia` -> Canvas rendering.
5.  **Scene Logic**: State machine for switching views.
6.  **Interactive Tools**: Whiteboard & Chat mockups.
