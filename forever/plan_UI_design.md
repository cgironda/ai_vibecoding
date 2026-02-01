# GUI Plan: FOREVER Landing Page (MVP)
**Basis:** `master_plan_simple.md` & `PDR.md`
**Philosophy:** Minimalist, Direct, High-Contrast.

---

## 1. Visual Identity (The "Theme")

### Typography
*   **Primary Font (Headings):** System Sans-Serif (`Inter`, `Roboto`, or `system-ui`). Bold, tight tracking.
*   **Secondary Font (Body):** System Sans-Serif. High readability, relaxed line-height (1.5).
*   **Scale:**
    *   `H1` (Hero): 3rem (Mobile) / 4.5rem (Desktop) — Heavy weight.
    *   `H2` (Section): 2rem (Mobile) / 3rem (Desktop).
    *   `Body`: 1rem (16px base) minimum.

### Color Palette
*   **Background:** `#FFFFFF` (White) or `#F8F9FA` (Off-white softness).
*   **Text (Primary):** `#111827` (Near-Black) — Never pure black.
*   **Text (Secondary):** `#4B5563` (Dark Gray) for subheads/body.
*   **CTA (Primary):** `#2563EB` (Bright Blue) or `#DC2626` (Red) — distinct from brand colors.
*   **CTA Text:** `#FFFFFF` (White) for contrast.

---

## 2. Layout & Structure (Mobile-First)

### The Stack (Vertical Column)
1.  **Header/Hero:**
    *   Logo/Brand Name (Top Left).
    *   Headline (Centered or Left-aligned).
    *   Sub-headline.
    *   **Primary CTA Button**.
    *   Hero Image (Below text on mobile, Right side on desktop).
2.  **Problem Section:**
    *   Gray background `#F3F4F6` to distinguish from Hero.
    *   3-bullet list of "Pain Points" with simple icons (SVG).
3.  **Solution Section:**
    *   White background.
    *   Headline + Short Paragraph.
4.  **Proof Section:**
    *   Testimonial Card: Border `#E5E7EB`, Padding 1.5rem.
    *   Quote marks icon.
5.  **Offer Section:**
    *   **Main Card:** Shadow `box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1)`.
    *   Product Image.
    *   Price (Large) + "What's included" list.
    *   **Primary CTA Button** (Repeated).
6.  **Final CTA:**
    *   Short reminder headline (1 line max).
    *   **Primary CTA Button** only (no extra fields).
7.  **Footer:**
    *   Simple text links (Privacy, Terms).
    *   `mailto` Support link.
    *   Copyright.

---

## 3. Component Specifications

### Buttons (CTA)
*   **Shape:** Rounded corners (`border-radius: 8px`).
*   **Padding:** Vertical `12px`, Horizontal `24px` (Finger-sized touch target).
*   **State - Hover:** Darken background by 10%.
*   **State - Active:** Scale down 0.98 (Tactile feedback).
*   **Text:** Bold, Uppercase or Capitalized.

### Images
*   **Hero:** `object-fit: cover`. Max-width 100%. Rounded corners `12px`.
*   **Icons:** Simple SVG outline style (e.g., Heroicons).

### Links (Footer)
*   **Style:** Underlined or darker color to signal "clickable".
*   **State - Focus:** Visible outline (same as buttons).
*   **State - Hover:** Slight color shift only.

### Spacing (The "Air")
*   **Section Padding:** 4rem (64px) vertical minimum.
*   **Element Gap:** 1.5rem (24px) between Headline and Body.
*   **Max Width:** Content container `max-width: 1100px`.
*   **Text Measure:** Limit long text blocks to ~60–70ch.

---

## 4. Mobile vs. Desktop Responsiveness

| Feature | Mobile (Default) | Desktop (>768px) |
| :--- | :--- | :--- |
| **Grid** | 1 Column | 1 Column (Process) / 2 Columns (Hero) |
| **Padding** | 1.5rem (24px) outer | 4rem (64px) outer |
| **Font H1** | 3rem | 4.5rem |
| **Nav/Menu** | Hidden/None | Hidden/None (As per PDR) |

---

## 5. Accessibility (A11y) Rules
*   **Focus Ring:** Visible outline on Tab focus for all Buttons/Links.
*   **Contrast:** CTA Background vs Text must be > 4.5:1 ratio.
*   **Alt Text:** Required on Product Image and Hero Image.
*   **Icons:** Mark decorative SVGs as `aria-hidden="true"`.

---

## 6. Implementation Notes for Dev
*   Use `flexbox` for main layout.
*   Use CSS Variables for Colors for easy theming (`--color-primary-cta`).
*   No external design libraries (Bootstrap/Tailwind) needed if doing Vanilla; otherwise, use utility classes mapping to these specs.
