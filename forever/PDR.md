# Product Definition & Requirements (PDR)
## Product: FOREVER Landing Page (MVP)
Version: 0.1  
Scope: One landing page. One goal.  
Status: Ready to build.

---

## 1. Objective

Build a **single, high-clarity landing page** that explains one FOREVER offer and drives users to **one primary action**.

**Primary goal:**  
→ User clicks the main CTA and completes checkout (or reaches checkout).

---

## 2. Target User

- One primary persona (defined in **one sentence**)
- First-time or lightly aware visitor
- Primarily mobile

No secondary personas. No edge cases.

---

## 3. Page Structure (Required Order)

Mobile-first, vertical scroll only:

1. **Hero**
   - Clear benefit headline
   - One primary CTA button

2. **Problem**
   - What is wrong today
   - Why current solutions fail

3. **Solution**
   - What FOREVER is
   - How it solves the problem (high level)

4. **Proof**
   - Reviews, facts, or simple logic
   - Short and credible

5. **Offer**
   - What the user gets
   - Price or clear path to price

6. **Final CTA**
   - Same action as the first CTA

7. **Footer**
   - Privacy / Terms links
   - Support email (mailto:)

**Constraints**
- No navigation menu
- No extra sections

---

## 4. Content Rules

- Headline = clear benefit (no slogans)
- Copy speaks to **“you”**, not “we”
- One CTA text only (repetition allowed)

**Example CTA:**  
“Get FOREVER Now”

---

## 5. Design Requirements

- Mobile-first layout
- Clean, readable typography
- High-contrast CTA button
- Real images if available (product or usage)
- Favicon (Emoji or simple SVG)

No animations required. No complex interactions.

---

## 6. Technical Requirements

- Stack: HTML/CSS or lightweight framework (Astro / Next.js)
- Hosting: Netlify or Vercel
- Checkout: Direct link (e.g., Stripe) or simple form
- Basic SEO: Meta Title, Description, Open Graph Image
- Resilience: Custom 404 page (or redirect to home)

---

## 7. Performance Requirements

- Load time under **2 seconds**
- Images compressed
- No heavy or blocking scripts

---

## 8. Legal & Tracking (Minimum)

**Required**
- Privacy Policy link
- Terms of Service link

**Analytics**
- Pageviews
- CTA clicks

No advanced tracking.

---

## 9. Launch Checklist

- Page deployed to production
- CTA works end-to-end
- Analytics confirmed
- One real click/transaction test completed

---

## 10. Post-Launch Rule

After ~100 visitors:
- Low scroll → fix Hero
- Scroll but no clicks → fix Offer

Change **one thing at a time**.

---

## Definition of Done

- Page is live
- Message is clear
- CTA works
- Data is collected
