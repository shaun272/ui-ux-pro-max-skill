# Tru-Trac Website — Design Review & Execution Plan

**Target:** top 0.25% of B2B websites — design, content, motion, design system, UX, navigation pathways, business results, architecture, SEO, and AI search.

**Subject:** https://tru-trac.vercel.app/en (Next.js + Tailwind v4 + shadcn/ui)
**Review date:** 2026-07-04
**Method:** audit of shipped HTML + compiled CSS across `/en`, `/en/products/belt-trackers`, `/en/request-assessment`, `/en/knowledge`; computed WCAG contrast on extracted design tokens; measured network payloads. Evidence in [`01-audit-evidence.md`](./01-audit-evidence.md).

---

## Verdict

The site is already in roughly the **top 5–10%** of B2B websites: real 3-tier design tokens, surface-aware color pairs, fluid type scale, deep schema.org coverage (Product, FAQPage, BreadcrumbList), full landmark semantics, and a serious reduced-motion/forced-colors implementation.

To reach the **top 0.25%**, the gaps are not foundations — they are:

1. **Conversion architecture** — 5 CTAs funnel into a ~24-field single-step form labeled by placeholders only.
2. **Motion craft** — CSS transitions only; no signature interaction. The Zones of Control concept is unrealized as an experience.
3. **Performance calibration** — a 610 KB LCP hero served at w=3840 to every device; 1 of 102 images has explicit dimensions.
4. **Accessible color calibration** — the default brand red fails WCAG AA as text/UI (an AA-safe variant already exists in the tokens but is not the default).
5. **Measurement** — no way to attribute business results to pathways (instrument before changing anything).

## Scorecard

| Dimension | Now | Top-0.25% bar | Score |
|---|---|---|---|
| Design system | 3-tier tokens, surface-aware fg, fluid scale | + documented, versioned, zero AA failures | ~90% |
| Content / IA | Strong editorial voice, problem→zone→component→proof | + quantified proof, persona pathways | ~75% |
| SEO (classic) | Split sitemaps, Product/FAQ/Breadcrumb schema, canonical | + og:image everywhere, hreflang, link graph | ~80% |
| AI search (AEO) | FAQPage schema baseline | Entity consistency, llms.txt, citable stat pages | ~60% |
| Performance | 279 KB gz JS (good); 610 KB LCP hero, 1/102 imgs sized | LCP <1.8s p75 field, CLS <0.05, CI budgets | ~50% |
| Accessibility | Skip link, 102/102 alt, reduced-motion, forced-colors | Zero contrast failures, zero placeholder-only labels | ~80% |
| Motion | CSS transitions, one rotating headline | Signature scroll choreography, 60fps, RM parity | ~30% |
| Conversion | Long single-step form, placeholder labels | Multi-step commitment gradient, persona routing, funnels | ~35% |
| Trust / proof | Logos + case studies; "Imagery pending" live; unquantified stats | Quantified, attributable outcomes on every proof | ~55% |

## The plan (phases, each item has a "done-when")

### Phase 0 — Stop the bleeding (days) → [`02-phase-0-implementation.md`](./02-phase-0-implementation.md)
1. Promote AA-safe red (`#d52121`) to default text/interactive token; fix `--fg-on-red-soft` (2.65:1 → ≥4.5:1). *Done when scripted token-pair check reports zero AA failures.*
2. Homepage `og:image` (product pages have generated ones; the homepage has none). *Done when every route validates in an OG debugger.*
3. Remove live "Imagery pending" placeholders from case-study cards. *Done when no placeholder copy in production HTML.*
4. Visible persistent labels on all assessment-form fields (currently 1 `<label>` for ~24 inputs). *Done when every field has label + below-field error.*
5. Hero LCP: responsive `sizes` so mobile stops downloading the 3840px/610 KB asset. *Done when LCP asset ≤200 KB mobile / ≤350 KB desktop.*
6. CLS guards: `aspect-ratio` on image wrappers; `min-h-dvh` not `100vh`. *Done when lab CLS <0.05 on home, products, case studies.*

### Phase 1 — Conversion architecture (weeks 1–3, highest business ROI)
7. Rebuild `/request-assessment` as a 3-step flow (problem → site context → contact) with progress, back nav, autosave. *Done when step-level analytics beat the measured baseline.*
8. Persona pathways: "I have a belt problem" / "I'm specifying a project" / "Become a partner" entries in hero + nav. *Done when each persona converts in ≤2 clicks with separate funnels.*
9. Quantify every case study (downtime saved, belt-life %, tonnage). *Done when 100% of published case studies carry ≥1 quantified outcome.*
10. Instrument first: RUM CWV + CTA/step/nav events wired to CRM. *Done when "which pathway produced last month's assessment requests" is answerable from a dashboard.*

### Phase 2 — Performance & architecture (weeks 2–4)
11. Responsive images everywhere (`sizes` per slot, AVIF-first). *Done when no image downloads >1.5× rendered size.*
12. Lighthouse CI budgets on every PR: LCP ≤1.8s, CLS ≤0.05, INP ≤200ms, JS ≤300 KB gz. *Done when a regressing PR fails the build.*
13. Field-data gate: p75 CWV green for 90 consecutive days; trim the 493 KB HTML document if field LCP demands it.

### Phase 3 — SEO + AI search (weeks 3–8)
14. hreflang + x-default the moment a second locale ships (`/en` implies i18n; es/pt/fr are the mining geographies).
15. Entity building: Organization `sameAs`/`foundingDate`/`areaServed`, `llms.txt`, HowTo/TechArticle on knowledge content. *Done when brand + category queries in ChatGPT/Perplexity/Gemini cite tru-trac.com (fixed monthly query set).*
16. Knowledge hub as topical moat: one definitive page per failure mode, interlinked problem → knowledge → product → case study.
17. Selector tools per category + spec tables with `additionalProperty` schema + glossary.

### Phase 4 — Signature motion (weeks 5–10)
18. The Zones of Control (01–05) as a scroll-driven pinned sequence — GSAP ScrollTrigger, transform/opacity only. *Done when 60fps on mid-range Android with full reduced-motion fallback, live on `/` and `/solutions/zones-of-control`.*
19. Motion tokens: stagger 30–50ms, exit ≈65% of enter, shared-element continuity; no ad-hoc durations. 
20. Micro-interaction pass: CTA press states, stat count-ups (tabular-nums already present), crossfade transitions. Restraint — industrial confidence, not agency flash.

### Phase 5 — The proof loop (ongoing)
21. Quarterly re-audit against this scorecard + WCAG 2.2 AA re-verification.
22. A/B the six rotating hero value props; let data pick the lead.
23. Design-system documentation page so the standard survives team changes.

## Definition of done, in numbers

- **CWV:** p75 field LCP <1.8s, INP <200ms, CLS <0.05 — 90-day green streak
- **A11y:** WCAG 2.2 AA, zero automated + zero manual failures
- **SEO/AEO:** top-3 organic for the six failure-mode queries in target geos; cited by ≥2 of 3 major AI engines on category queries
- **Business:** assessment-request completion up ≥40% vs first-measured baseline; every submission attributable to a pathway
- **Craft:** Zones interaction at 60fps; external validation via Awwwards/CSSDA submission

**Sequencing logic:** instrument (10) → credibility fixes (Phase 0) → conversion (Phase 1) → craft (Phase 4). Motion polish on an unmeasured funnel with a placeholder-labeled form is decoration on a leak.
