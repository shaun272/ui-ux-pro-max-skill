# Audit Evidence — tru-trac.vercel.app

All findings measured 2026-07-04 against deployment `dpl_8qED2ZWV2GAAHQtgWAqhmac8naJX`. Pages audited: `/en`, `/en/products/belt-trackers`, `/en/request-assessment`, `/en/knowledge`, plus `robots.txt` and `sitemap.xml`.

## 1. Design tokens (extracted from compiled CSS)

### Brand color
| Token | Value |
|---|---|
| `--tt-red` | `#e52a2a` (default) |
| `--tt-red-aa` | `#d52121` (AA-safe variant, exists but not default) |
| `--tt-red-press` | `#c81f1f` |
| `--tt-red-bright` | `#f44` |
| `--tt-ink` / `--tt-ink-soft` | `#111` / `#5a5a5a` |
| `--tt-paper` / `--tt-line` | `#f5f5f5` / `#e0e0e0` |
| Semantic layer | `--fg-on-paper`, `--fg-on-card`, `--fg-on-red`, `--fg-on-red-soft: #ffffffc7`, `--bg-atmosphere`, `--hairline` |

### Type (all fluid, single family: Inter; `--font-display` and mono alias sans)
`--fs-eyebrow 12px` · `--fs-body 16px` · `--fs-h3 22px` · `--fs-h2 clamp(28,2.6vw,40)` · `--fs-h1 clamp(40,4.5vw,64)` · `--fs-display clamp(56,7vw,96)` · `--fs-mega clamp(72,9vw,120)` · line-height .92–.96 display / 1.55 body · weights 300–900

### Space / radius / motion
`--space-1..10` = 4/8/12/16/24/32/48/64/96/128 · `--page-pad-x clamp(20,4vw,64)` · `--section-pad-y clamp(72,6vw,96)` · container 1480px · radius: button 4px, cards clamp(14–32), pill 999 · `--dur-1 .16s`, `--dur-2 .28s`, ease `cubic-bezier(.32,.72,0,1)`

## 2. WCAG contrast (computed, WCAG 2.x relative luminance)

| Pair | Ratio | Verdict |
|---|---|---|
| `--tt-red #e52a2a` on white | **4.47** | FAIL as normal text (needs 4.5); OK large-text/UI |
| `--tt-red #e52a2a` on paper `#f5f5f5` | **4.10** | FAIL as normal text |
| White on `#e52a2a` (primary buttons) | **4.47** | FAIL as normal text |
| `--fg-on-red-soft` (78% white) on red | **2.65** | **FAIL outright** |
| `--tt-red-aa #d52121` on white | 5.16 | PASS AA |
| `--tt-red-aa` on paper | 4.73 | PASS AA |
| White on `--tt-red-press #c81f1f` | 5.72 | PASS AA |
| `--tt-ink #111` on paper | 17.32 | PASS |
| `--tt-ink-soft #5a5a5a` on paper / white | 6.33 / 6.90 | PASS |
| `--tt-line #e0e0e0` on white | 1.32 | decorative only — never sole boundary of interactive element |

## 3. Performance

- **Hero LCP asset: 609,681 bytes**, requested at `/_next/image?...&w=3840&q=70` with **no `srcset`/`sizes`** — every device including mobile downloads the 3840px rendition. (`srcset` count across the homepage: **0** on 102 images.)
- Client logos are small (~5 KB; optimizer caps at source size) — the LCP hero is the real cost.
- **1 of 102 images** declares width/height → CLS risk; hero uses `100vh` (not `dvh`) → mobile URL-bar jump.
- `loading="lazy"` on 101/102 images; hero correctly `fetchPriority="high"` — good.
- JS: **279 KB gzipped across 18 chunks** — healthy for Next.js.
- HTML document: **493 KB** (large inline SSR payload; revisit only if field LCP/TTFB is poor).
- Fonts: single Inter with `font-display: swap` + fallback family, 1 preload — good.

## 4. Accessibility

**Strong:** skip-to-content link; **102/102 images have `alt`** (17 correctly empty); 51 `aria-label`s; `role=tab/tablist/dialog`; single `<h1>`, clean h2→h3 (no h4+ skips because none used); landmark set complete (nav/header/main/footer/section); thorough `prefers-reduced-motion` (global reset + per-component); `forced-colors` support; `(hover:hover)` gating; rotating H1 toggles `aria-hidden` correctly; `tabular-nums` on stats; SVG icons only.

**Failures:**
- Contrast items in §2.
- **Assessment form: ~24 inputs, 1 `<label>`** — fields labeled by `placeholder` only (e.g. `name="name"` input has no label/aria-label). Placeholders vanish on input; WCAG 3.3.2 / 1.3.1 failure and a completion-rate drag.
- `role="text"` (non-standard) appears twice.
- `:focus{outline:none}` global reset is compensated by `focus-visible:` ring utilities on shadcn components, but custom `tt-btn`/link styles must be spot-checked for keyboard focus visibility.

## 5. SEO / AI search

**Present:** canonical → `www.tru-trac.com`; robots.txt sane (blocks `/cms`, `/auth`, `/api`); **sitemap index split by type** (pages/products/knowledge/case-studies/global/legal); JSON-LD on homepage (Organization + WebSite + WebPage); **product pages: Product ×4, FAQPage (6 Q&A), BreadcrumbList, Brand, ContactPoint**; knowledge: CollectionPage + ItemList + FAQPage; per-route generated `opengraph-image` on product pages; `theme-color` for both schemes.

**Missing / weak:**
- **Homepage has NO `og:image`** (twitter:card is set to summary_large_image with no image to show) — the most-shared URL renders bare in LinkedIn/WhatsApp/Slack previews.
- **No hreflang** anywhere despite `/en` prefix (single locale today; required the day locale #2 ships).
- Organization schema lacks `sameAs` (no LinkedIn etc.), thin entity signals for AI engines; no `llms.txt`.
- OG url/canonical point at `www.tru-trac.com` while the Vercel host serves the content — correct at launch, but verify no indexable duplicate of the vercel.app host (add `x-robots-tag: noindex` on the preview domain or Vercel's deployment-protection equivalent).

## 6. Content / conversion observations

- H1 rotates six value props ("conveyor uptime / belt availability / fewer unplanned stops / longer belt life / cleaner safer conveyors / controlled performance") — good material for A/B; currently unmeasured.
- IA: problem cards (6 failure modes) → Zones of Control (01–05) → components → case studies → partner CTA. Editorially strong.
- **"Imagery pending" placeholder copy is live** in the case-study section of the production homepage.
- `/en/request-assessment` is referenced by 5 CTAs; the form is single-step with ~24 fields (10 text, 10 radio, number/tel/email), no step indicator, placeholder-only labeling.
- Case studies exist (e.g. `copper-tripper-conveyor`) but outcomes on the homepage are not consistently quantified.
- Nav labels ≠ slugs (Components → `/en/products`, Cases → `/en/case-studies`, Company → `/en/about`) — fine, but pick one vocabulary and use it in both nav and URLs when convenient.

## 7. Motion

- No animation library shipped (no GSAP/Framer in bundles); motion is CSS transitions + one keyframe headline rotator and zone-panel reveals.
- Reduced-motion handling for what exists is exemplary.
- Gap is craft, not correctness: nothing scroll-driven, no signature interaction, no shared-element continuity.
