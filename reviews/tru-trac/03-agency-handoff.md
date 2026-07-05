# Agency Handoff Appendix — Production Inputs the Demo Cannot Supply

The reference build (`demo/index.html`) is the accepted design standard. The following inputs are required from Tru-Trac / the agency to take it from reference to production. Each placeholder in the demo is marked in-page.

## 1. Photography commission (highest visual ROI)
- **Per-zone scenics (5):** wide, purpose-shot frames for Load/Carry/Discharge/Return/Monitoring; the demo currently redeploys diagnostic photos as scenics (tunnel + overland frames are the quality bar). Night or dust-light conditions matched to the hero.
- **Product renders (6 families):** consistent camera angle (¾ high-front), consistent lighting rig, consistent shadow treatment, transparent background, 2000px+. Current renders vary in angle and lighting.
- **People:** 2–3 authentic frames of Tru-Trac engineers on site (assessment/installation moments) for the engagement band and About surface.

## 2. Verified content
- **Case-study figures:** replace the three placeholder stats (63% / 2.1× / R18m) with audited numbers + client permission status. Keep commodity + geography format.
- **Field quotes:** two real attributed quotes (role · commodity · region). Demo copies are placeholders and marked as such.
- **Brand typography reconciliation:** the official wordmark uses a rounded geometric sans; the demo's display voice is Archivo (variable width). Confirm the corporate face and either license a display companion or ratify Archivo as the campaign voice.

## 3. Technical port (spec'd in porting notes in-page + 02-phase-0-implementation.md)
- GSAP ScrollTrigger for the zones sequence (`pin, scrub:1, end:'+=150%'`), `next/image` with per-slot `sizes` (data-URIs are single-file packaging only), font subsetting, real form endpoint + CRM wiring, analytics events per CTA/step (see plan §10).
- Reduced-motion behavior, no-JS fallbacks, focus management, and the `?present` flag behavior must survive the port as-is.

## 4. Assets delivered with this demo
- Official roundel (32/48/96px PNG) and 440px lockup embedded; source files supplied by client upload (roundel 1409×1417) and site (`/images/logos/logo-red-full.png` 2948×1056).
