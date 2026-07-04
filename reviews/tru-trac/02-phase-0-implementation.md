# Phase 0 — Developer Implementation Spec

Hand this file to the site's development team. Each fix states the problem, the exact change, and the acceptance check. All are low-risk, no redesign, shippable in one sprint. File paths are indicative (Next.js App Router conventions) — adapt to the actual repo layout.

---

## 0.1 Promote the AA-safe red

**Problem:** `--tt-red #e52a2a` is the default text/interactive red at 4.47:1 on white and 4.10:1 on paper — below the 4.5:1 WCAG AA threshold for normal text. `--fg-on-red-soft: #ffffffc7` is 2.65:1 on red — a hard failure. The AA-safe `--tt-red-aa #d52121` already exists in the tokens.

**Change (global CSS / Tailwind theme):**

```css
:root {
  /* text + interactive red: AA-safe becomes the default */
  --tt-red-text: #d52121;        /* 5.16:1 on white, 4.73:1 on paper */
  --tt-red-display: #e52a2a;     /* large display type (>=24px/19px bold) and decor ONLY */
  --tt-red: var(--tt-red-text);  /* flip the default */

  /* soft copy on red surfaces: raise from 78% to >=93% white */
  --fg-on-red-soft: rgba(255 255 255 / .93);  /* verify >=4.5:1 against the actual surface red */
}
```

Sweep for hardcoded `#e52a2a` in component classes (e.g. `focus:border-tru-red` compiles the hex directly) and re-point them at the token.

**Accept when:** a scripted check over every fg/bg token pair reports zero AA failures. Suggested script (run in CI, `scripts/check-contrast.mjs`):

```js
// contrast(fg, bg) >= 4.5 for text pairs, >= 3.0 for large-text/UI pairs
const PAIRS = [
  ['--tt-red-text', '--tt-white', 4.5],
  ['--tt-red-text', '--tt-paper', 4.5],
  ['--tt-white',    '--tt-red-press', 4.5],   // button pressed
  ['--fg-on-red-soft-resolved', '--tt-red', 4.5],
  ['--tt-ink-soft', '--tt-paper', 4.5],
  // ...extend to every published pair
];
```

---

## 0.2 Homepage og:image

**Problem:** product pages generate `opengraph-image`; the homepage — the most-shared URL — has none, and `twitter:card=summary_large_image` is declared with no image.

**Change:** add `app/[locale]/opengraph-image.tsx` (or a static `opengraph-image.png`, 1200×630) using the brand: paper background, red eyebrow, "Engineered conveyor uptime." + logo. Next.js will emit `og:image` + `twitter:image` automatically.

**Accept when:** every indexable route shows an image in an OG debugger (opengraph.xyz / LinkedIn Post Inspector), including `/en`.

---

## 0.3 Remove "Imagery pending" placeholders

**Problem:** live case-study cards on the homepage render "Imagery pending — Industrial operation …" to prospects.

**Change:** in the case-study card component, if the image asset is missing, fall back to an on-brand branded cover (red atmosphere + commodity label) — never placeholder prose. Remove the string from the CMS entries.

**Accept when:** `curl -s https://<host>/en | grep -ci "imagery pending"` returns 0.

---

## 0.4 Visible labels on the assessment form

**Problem:** `/en/request-assessment` has ~24 inputs and one `<label>`; fields rely on `placeholder`, which disappears on input (WCAG 3.3.2/1.3.1; hurts completion).

**Change (pattern per field):**

```tsx
<div className="field">
  <label htmlFor="site" className="text-sm font-medium text-[var(--fg-on-paper)]">
    Site / operation <span aria-hidden>*</span>
  </label>
  <input id="site" name="site" required
         aria-describedby="site-hint site-error"
         className="..." />
  <p id="site-hint" className="text-xs text-[var(--fg-on-paper-soft)]">e.g. Mine name, terminal, plant</p>
  <p id="site-error" role="alert" className="text-xs text-[var(--tt-red-text)] hidden">…</p>
</div>
```

Rules: label always visible; move example text to a persistent hint below (keep `placeholder` only as a supplement); error below the field via `role="alert"`; radio groups wrapped in `<fieldset><legend>`; validate on blur, not keystroke; on submit error, focus the first invalid field.

**Accept when:** every field has a visible persistent label; axe-core reports zero label violations on the page.

---

## 0.5 Hero LCP — responsive sizes

**Problem:** hero is 610 KB at `w=3840` for all devices; zero `srcset` sitewide.

**Change:** the hero `<Image>` must declare `sizes` (and every image slot gets an honest `sizes`):

```tsx
<Image
  src="/images/homepage/hero/homepage-hero.webp"
  alt="…"
  fill
  priority
  quality={60}
  sizes="100vw"           // hero: full-viewport — optimizer now emits a srcset ladder
/>
// logos / cards / thumbnails — size to the slot, not the viewport:
sizes="(max-width: 768px) 45vw, 180px"
```

If the component currently bypasses `next/image`'s srcset by passing a fixed `w`, remove that. Consider a mobile-specific hero crop (art direction) via `<picture>` if the 21:9 crop wastes pixels on portrait screens.

**Accept when:** requested hero rendition ≤200 KB on a 390px viewport and ≤350 KB at 1440px (verify in devtools network panel); no image sitewide downloads >1.5× its rendered size.

---

## 0.6 CLS guards

**Problem:** 1 of 102 images declares dimensions; hero section uses `100vh`.

**Change:**
- Give every image wrapper an `aspect-ratio` (or use `next/image` `width`/`height`/`fill`+sized container) so space is reserved pre-load.
- Hero: `min-height: 100dvh` (with `100vh` fallback line above it for old browsers).

```css
.hero { min-height: 100vh; min-height: 100dvh; }
.card__media { aspect-ratio: 3 / 2; }
```

**Accept when:** Lighthouse lab CLS < 0.05 on `/en`, `/en/products/belt-trackers`, `/en/case-studies`.

---

## 0.7 Bonus hygiene (30 minutes total)

- Replace non-standard `role="text"` (2 occurrences) with plain spans or `aria-label` on the parent.
- Ensure the vercel.app host is non-indexable (Vercel deployment protection or `x-robots-tag: noindex` on non-production domains) so it never competes with `www.tru-trac.com`.
- Unify breakpoint units (CSS uses both `rem`-based Tailwind screens and raw `900px/1100px` customs) — pick one scale.

---

## CI gate to lock it in (Phase 2 preview)

Add Lighthouse CI so Phase 0 can't regress:

```yaml
# .github/workflows/lighthouse.yml (or Vercel integration)
assertions:
  categories:performance: ["error", { minScore: 0.9 }]
  largest-contentful-paint: ["error", { maxNumericValue: 1800 }]
  cumulative-layout-shift: ["error", { maxNumericValue: 0.05 }]
  total-byte-weight: ["warn", { maxNumericValue: 1500000 }]
```
