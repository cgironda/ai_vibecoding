# FOREVER Landing Page — Step-by-Step Master Build Plan
Version: 1.0  
Owner: Marketing / Growth Lead  
Scope: Single landing page (educate → persuade → sell) + measurement + iteration

---

## 0) Operating Rules (Enforced)
- Single primary persona.
- Single primary conversion goal.
- Every page section must be tagged as [E], [P], or [S].
- No section ships without proof, mechanism, or justification.
- No “catalog mode” (limit to 1–3 featured offers).

**Output:** Scope locked; deviations require explicit change request.

---

## 1) Define Conversion Strategy (Day 0)

### Step 1.1 — Choose the primary conversion type
**Input:** Business model + fulfillment capability  
**Action:** Select ONE:
- A) Direct purchase (buy now)
- B) Lead capture (consultation / guided purchase)
- C) Hybrid (not recommended unless you can run tests)

**Output:** `conversion_goal` (single sentence)  
**Acceptance criteria:** Conversion goal is unambiguous and measurable.

---

### Step 1.2 — Define conversion event and KPI
**Input:** Selected conversion type  
**Action:**
- Define the exact event (purchase / form submit / booking click)
- Define KPI target (baseline if unknown)

**Output:** `success_metrics.md` (KPI + event definition)  
**Acceptance criteria:** Analytics event can be implemented without interpretation.

---

## 2) Lock Persona + Awareness Level (Day 0–1)

### Step 2.1 — Select a single primary persona
**Input:** Current customer patterns + sales feedback  
**Action:** Write persona in 10 lines:
- Who they are
- What they want
- What they fear
- What stops them buying

**Output:** `persona_primary.md`  
**Acceptance criteria:** Persona is singular; not “everyone who wants health.”

---

### Step 2.2 — Select awareness stage
**Input:** Traffic sources + persona maturity  
**Action:** Pick ONE:
- Unaware
- Problem-aware
- Solution-aware
- Ready-to-buy

**Output:** `awareness_stage` (one label)  
**Acceptance criteria:** Chosen stage matches expected traffic sources.

---

## 3) Proof Asset Collection (Day 1–3)

### Step 3.1 — Inventory proof assets
**Input:** Company materials, supplier info, certifications, internal data  
**Action:** Collect and categorize:
- Certifications / compliance
- Manufacturing / sourcing documentation
- Longevity / scale metrics
- Testimonials (with permission)
- Any clinical / scientific references (if you have them)

**Output:** `proof_inventory.xlsx` (or table in markdown)  
**Acceptance criteria:** Each claim has at least one supporting asset or is removed.

---

### Step 3.2 — Approve “claim boundaries”
**Input:** Proof inventory  
**Action:** Create a list of:
- Allowed claims (substantiated)
- Forbidden claims (unsubstantiated / risky)

**Output:** `claim_boundaries.md`  
**Acceptance criteria:** No claim can ship outside allowed list.

---

## 4) Offer Architecture (Day 2–4)

### Step 4.1 — Choose 1–3 featured offers
**Input:** Catalog + margin + fulfillment + persona needs  
**Action:** Select:
- One flagship product or starter bundle
- Optional: 1–2 supporting options

**Output:** `offer_stack.md`  
**Acceptance criteria:** Maximum 3 purchase choices on page.

---

### Step 4.2 — Define “guided purchase logic”
**Input:** Offer stack  
**Action:** Write “who it’s for” rules:
- Persona symptoms → recommended offer
- Contraindications or “not for you” notes (if applicable)

**Output:** `offer_guidance.md`  
**Acceptance criteria:** User can self-select without confusion.

---

## 5) Page Outline + Section Contracts (Day 3–5)

### Step 5.1 — Build the full section map (contract-based)
**Input:** Control-system architecture + offer stack  
**Action:** Define the page sections in order, each with:
- Tag: [E]/[P]/[S]
- Purpose statement
- Required proof type
- Required CTA type

**Output:** `page_map.md`  
**Acceptance criteria:** Every section has a role and required deliverables.

---

### Step 5.2 — Define H1/H2/H3 structure (SEO skeleton)
**Input:** Keyword theme + page map  
**Action:** Create heading outline:
- One H1
- H2 for each major section
- H3 for sub-arguments

**Output:** `seo_outline.md`  
**Acceptance criteria:** Headings reflect intent; no duplication.

---

## 6) Wireframe Build (Day 4–7)

### Step 6.1 — Low-fidelity wireframe (mobile-first)
**Input:** page_map.md  
**Action:** Produce mobile wireframe for:
- Hero
- Problem
- Education
- Differentiation
- Proof
- System overview
- Offer
- Objections
- Final CTA

**Output:** `wireframe_v1.png` (or Figma link)  
**Acceptance criteria:** One primary CTA path; minimal navigation.

---

### Step 6.2 — Wireframe review against funnel logic
**Input:** wireframe_v1  
**Action:** Check:
- Scroll tells a story: educate → persuade → sell
- CTA appears every 1–2 screens
- No section looks like a catalog

**Output:** `wireframe_v2` (approved)  
**Acceptance criteria:** Approval checklist passes; otherwise revise.

---

## 7) Copy Development (Day 6–10)

### Step 7.1 — Write Section 1–3 copy ([E])
**Input:** persona + awareness stage + claim boundaries  
**Action:** Draft:
- Hero value proposition (no slogans)
- Problem statements (specific)
- Category education (mechanism-focused)

**Output:** `copy_E.md`  
**Acceptance criteria:** Can be read standalone and still make sense.

---

### Step 7.2 — Write Section 4–6 copy ([P])
**Input:** proof assets + differentiation mechanisms  
**Action:** Draft:
- Differentiators with “why it matters”
- Proof layer (testimonials/metrics)
- Product system overview (guided)

**Output:** `copy_P.md`  
**Acceptance criteria:** Every differentiator has mechanism + proof reference.

---

### Step 7.3 — Write Section 7–9 copy ([S])
**Input:** offer stack + objections list  
**Action:** Draft:
- Offer blocks (1–3)
- Objection responses
- Final CTA with next-step clarity

**Output:** `copy_S.md`  
**Acceptance criteria:** Decision is obvious; no hidden requirements.

---

## 8) Design Production (Day 8–14)

### Step 8.1 — Design system and UI kit
**Input:** brand guidelines + wireframe  
**Action:** Define:
- Typography scale
- Spacing system
- Button styles (single primary CTA style)
- Trust badge styling

**Output:** `ui_kit.fig`  
**Acceptance criteria:** CTA visually dominant; readable on mobile.

---

### Step 8.2 — High-fidelity design
**Input:** approved wireframe + copy  
**Action:** Produce final responsive designs:
- Mobile
- Tablet
- Desktop

**Output:** `design_final.fig` + exports  
**Acceptance criteria:** Design matches funnel logic; no distractions.

---

## 9) Development (Day 10–18)

### Step 9.1 — Implement page structure
**Input:** designs + seo_outline  
**Action:** Build HTML/CMS page with:
- Correct heading hierarchy
- Optimized images
- Minimal scripts

**Output:** Staging URL  
**Acceptance criteria:** Pixel-accurate; responsive; no layout shift.

---

### Step 9.2 — Implement performance optimization
**Input:** staging URL  
**Action:** Ensure:
- LCP under target
- Image compression & lazy loading
- Minimal blocking JS

**Output:** `performance_report.md`  
**Acceptance criteria:** Page speed meets <2.5s target (or documented constraints).

---

## 10) Analytics + Tracking (Day 12–18)

### Step 10.1 — Define event taxonomy
**Input:** conversion goal + funnel stages  
**Action:** Track:
- CTA clicks (per section)
- Scroll depth (25/50/75/90)
- Form submit / purchase
- Exit and bounce

**Output:** `analytics_events.md`  
**Acceptance criteria:** Events mapped to KPIs.

---

### Step 10.2 — Verify instrumentation
**Input:** staging page + analytics  
**Action:** Test event firing across devices.

**Output:** `tracking_validation.md`  
**Acceptance criteria:** No missing/duplicate events.

---

## 11) QA + Compliance (Day 16–20)

### Step 11.1 — Claim compliance review
**Input:** claim_boundaries + final copy  
**Action:** Confirm no forbidden claims shipped.

**Output:** `compliance_signoff.md`  
**Acceptance criteria:** 100% claims traceable to proof inventory.

---

### Step 11.2 — UX QA
**Input:** staging page  
**Action:** Check:
- Mobile readability
- CTA clarity
- Form friction (if any)
- Broken links

**Output:** `qa_log.md`  
**Acceptance criteria:** No high-severity issues.

---

## 12) Launch (Day 20)

### Step 12.1 — Production release
**Input:** signoffs complete  
**Action:** Deploy to production; confirm indexing settings.

**Output:** Live URL  
**Acceptance criteria:** No tracking regression; no rendering regression.

---

## 13) Post-Launch Iteration (Weeks 1–4)

### Step 13.1 — Baseline measurement
**Input:** first week data  
**Action:** Record:
- CVR
- CTR by section
- Scroll drop-offs
- Top objections inferred from exits

**Output:** `baseline_metrics_week1.md`  
**Acceptance criteria:** Clear bottleneck hypothesis exists.

---

### Step 13.2 — A/B test plan (one variable at a time)
**Input:** bottleneck hypothesis  
**Action:** Define tests:
- Hero proposition variant
- CTA wording
- Offer order
- Proof placement

**Output:** `ab_tests_backlog.md`  
**Acceptance criteria:** Each test has success metric and duration rule.

---

## Appendix A — Default Section Order (Reference)
1. Hero [E]
2. Problem [E]
3. Category Education [E]
4. Differentiation [P]
5. Proof [P]
6. System Overview [P→S]
7. Offer [S]
8. Objections [S]
9. Final CTA [S]

---

## Appendix B — Definition of “Done”
- Conversion goal is measurable and tracked
- Persona is singular and enforced
- Claims are substantiated
- Page loads fast and reads clearly on mobile
- Offer choice is limited and guided
- Funnel logic is obvious in scroll flow
