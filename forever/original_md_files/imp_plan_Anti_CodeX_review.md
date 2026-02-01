# FOREVER Landing Page — Step-by-Step Master Build Plan (v1.1)
Version: 1.1  
Owner: Marketing / Growth Lead  
Scope: Single landing page (educate → persuade → sell) + measurement + iteration  
Status: Execution-Ready, Governance-Enforced

---

## 0) Operating Rules (Enforced)

- Single primary persona.
- Single primary conversion goal.
- Every page section must be tagged as [E], [P], or [S].
- No section ships without proof, mechanism, or justification.
- No “catalog mode” (limit to 1–3 featured offers).
- Funnel logic overrides aesthetic or brand preference.

**Output:** Scope locked; deviations require explicit change request.

---

## 0.1) Decision Governance & Change Control (NEW)

### Decision Authority
- Conversion strategy: Business Owner
- Copy & claims: Marketing Lead
- Design & UX: Design Lead
- Launch readiness: Growth Lead

### Change Control Rules
- Pre-wireframe: changes allowed freely
- Post-wireframe approval: changes require written rationale
- Post-launch: changes only via experiments

### Tie-Break Rule
If opinions conflict, data or funnel logic wins.

**Output:** `decision_authority.md`  
**Acceptance criteria:** No unresolved decision ambiguity.

---

## 1) Define Conversion Strategy (Day 0)

### Step 1.1 — Choose the primary conversion type
**Input:** Business model + fulfillment capability  
**Action:** Select ONE:
- A) Direct purchase (buy now)
- B) Lead capture (consultation / guided purchase)
- C) Hybrid (only if test infrastructure exists)

**Output:** `conversion_goal` (single sentence)  
**Acceptance criteria:** Conversion goal is unambiguous and measurable.

---

### Step 1.2 — Define conversion event and KPI
**Input:** Selected conversion type  
**Action:**
- Define the exact event (purchase / form submit / booking click)
- Define KPI target (baseline if unknown)

**Output:** `success_metrics.md`  
**Acceptance criteria:** Analytics event implementable without interpretation.

---

## 1.3) Traffic Source Assumptions (NEW)

### Expected Primary Traffic Sources
Estimate realistic distribution:
- Organic search (%)
- Paid ads (%)
- Direct / brand (%)
- Distributor or partner referrals (%)

### Funnel Alignment Check
If dominant traffic source ≠ awareness stage:
→ Revise hero, problem framing, and early CTAs before build proceeds.

**Output:** `traffic_assumptions.md`  
**Acceptance criteria:** Awareness stage explicitly matches traffic reality.

---

## 2) Lock Persona + Awareness Level (Day 0–1)

### Step 2.1 — Select a single primary persona
**Input:** Customer data + sales feedback  
**Action:** Write persona in 10 lines:
- Who they are
- What they want
- What they fear
- What stops them buying

**Output:** `persona_primary.md`  
**Acceptance criteria:** Persona is singular and enforceable.

---

### Step 2.2 — Select awareness stage
**Input:** Traffic assumptions + persona maturity  
**Action:** Pick ONE:
- Unaware
- Problem-aware
- Solution-aware
- Ready-to-buy

**Output:** `awareness_stage`  
**Acceptance criteria:** Matches Step 1.3 assumptions.

---

## 3) Proof Asset Collection (Day 1–3)

### Step 3.1 — Inventory proof assets
**Input:** Internal and supplier materials  
**Action:** Collect and categorize:
- Certifications / compliance
- Manufacturing / sourcing
- Longevity / scale metrics
- Testimonials (permission confirmed)
- Clinical / scientific references (if any)

**Output:** `proof_inventory.md`  
**Acceptance criteria:** Every claim traceable or removed.

---

### Step 3.2 — Approve claim boundaries
**Input:** Proof inventory  
**Action:** Define:
- Allowed claims
- Forbidden claims

**Output:** `claim_boundaries.md`  
**Acceptance criteria:** No claim ships outside boundaries.

---

## 4) Offer Architecture (Day 2–4)

### Step 4.1 — Choose 1–3 featured offers
**Input:** Catalog + margins + persona needs  
**Action:** Select:
- One flagship offer
- Optional 1–2 secondary offers

**Output:** `offer_stack.md`  
**Acceptance criteria:** ≤3 choices.

---

### Step 4.2 — Define guided purchase logic
**Input:** Offer stack  
**Action:** Map:
- Persona conditions → recommended offer
- Clear “not for you if…” notes

**Output:** `offer_guidance.md`  
**Acceptance criteria:** User can self-select confidently.

---

## 4.3) Sales Escalation & Human Handoff (NEW)

### Trigger Conditions
- Multiple CTA clicks without conversion
- Long dwell time on objections
- Partial form completion

### Escalation Actions
- Offer consultation
- Guided recommendation
- Route to human advisor

### Ownership & SLA
- Lead owner defined
- Follow-up time defined

**Output:** `sales_handoff.md`  
**Acceptance criteria:** No high-intent lead is dropped.

---

## 5) Page Outline + Section Contracts (Day 3–5)

### Step 5.1 — Build section map
Each section defined with:
- Funnel tag [E/P/S]
- Purpose
- Required proof
- CTA type

**Output:** `page_map.md`  
**Acceptance criteria:** No untagged or purposeless section.

---

### Step 5.2 — SEO structure
**Input:** Keyword theme  
**Action:** Define:
- One H1
- Logical H2/H3 hierarchy

**Output:** `seo_outline.md`  
**Acceptance criteria:** Clear intent alignment.

---

## 6) Wireframe Build (Day 4–7)

### Step 6.1 — Mobile-first wireframe
**Output:** `wireframe_v1`  
**Acceptance criteria:** Single CTA path; no catalog layout.

---

### Step 6.2 — Funnel validation
**Output:** `wireframe_v2 (approved)`  
**Acceptance criteria:** Scroll tells educate → persuade → sell story.

---

## 7) Copy Development (Day 6–10)

### Step 7.1 — Educate copy [E]
**Output:** `copy_E.md`

### Step 7.2 — Persuade copy [P]
**Output:** `copy_P.md`

### Step 7.3 — Sell copy [S]
**Output:** `copy_S.md`

**Acceptance criteria:** Each section can stand alone and still convert.

---

## 8) Design & Tech Production (Day 8–14)

### Step 8.1 — UI Kit
**Output:** `ui_kit.fig`

### Step 8.2 — High-fidelity designs
**Output:** `design_final.fig`

### Step 8.3 — Tech stack & environment
**Output:** `tech_stack.md`

---

## 9) Development & Integrations (Day 10–18)

- Page implementation
- Performance optimization
- CRM, payments, webhooks

**Outputs:**  
- Staging URL  
- `performance_report.md`  
- `integration_map.md`

---

## 10) Analytics & Tracking (Day 12–18)

- Event taxonomy
- Instrumentation verification

**Outputs:**  
- `analytics_events.md`  
- `tracking_validation.md`

---

## 11) QA, Legal & Accessibility (Day 16–20)

- Claim compliance
- UX QA
- Privacy, ToS, cookie consent
- WCAG 2.1 AA basics

**Outputs:**  
- `qa_log.md`  
- `legal_audit.md`

---

## 12) Localization & Market Variants (NEW)

### Base Page
- Primary language
- Primary market assumptions

### Variant Rules
What may change:
- Claims phrasing
- Proof emphasis
- Disclaimers
- Payment methods

What must not change:
- Funnel order
- Core value proposition

**Output:** `localization_strategy.md`

---

## 13) Launch (Day 20)

**Output:** Live URL  
**Acceptance criteria:** No regression in tracking or rendering.

---

## 14) Post-Launch Measurement (Weeks 1–4)

### Baseline Reporting
- CVR
- Scroll drop-off
- CTA CTR
- Objection friction

**Output:** `launch_report.md`

---

## 15) Failure Criteria & Pivot Rules (NEW)

### Hard Failure Conditions
- CVR below X% after Y qualified sessions
- High bounce despite aligned traffic
- Persistent offer confusion after 2 iterations

### Pivot Options
- Reframe awareness stage
- Change primary offer
- Split into persona-specific pages

### Kill Rule
If no improvement after N controlled experiments, retire page.

**Output:** `failure_rules.md`

---

## 16) Continuous Optimization (Month 2+)

### A/B Testing Loop
- One variable at a time
- Falsifiable hypothesis only

**Output:** `experiment_log.md`

---

## 17) Knowledge & Enablement (NEW)

### Internal Enablement
- One-page internal brief for sales/support

### Knowledge Retention
- Central log of insights and decisions

**Outputs:**  
- `internal_one_pager.md`  
- `insights_log.md`

---

## Definition of “Done”
- Conversion goal achieved or falsified
- Claims substantiated
- Funnel logic intact
- Learnings captured
