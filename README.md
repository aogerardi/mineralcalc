# MineralCalc — Mineral Rights Royalty Income Calculator

A free, client-side web tool for mineral owners, landmen, and investors to model oil and gas royalty income, verify royalty checks, run sensitivity analysis, and evaluate lease bonus offers.

**Live site:** [mineralcalc.com](https://mineralcalc.com/)

---

## Features

- **Lease Analyzer** — drop in a lease PDF and get the key terms extracted plus a plain-English clause review (post-production deductions, Pugh clauses, pooling authority, shut-in caps, warranty of title, free use, continuous development, surface protection, payment timing, assignment). Parsed entirely in the browser with a vendored copy of pdf.js — the file is never uploaded
- **Scanned records are read too** — recorded county documents have no text layer, so when none is found the analyzer OCRs the pages with a vendored copy of tesseract.js. Still fully on-device, and loaded only when a scan is actually encountered
- **Oklahoma well lookup** — enter a Section-Township-Range and see the actual wells on record from OCC completion data: operator, formation, initial oil and gas test rates, lateral length, plus permits not yet drilled. One click calibrates the calculator's production inputs to real nearby wells
- **Live royalty income calculator** — monthly, annual, cumulative, and NPV projections update in real time as you type
- **Royalty decimal formula** — shows the exact math behind every number so you can verify and trust the output
- **Royalty check verifier** — enter a check amount and compare the implied decimal against your lease terms to spot underpayment
- **Two decline models** — exponential (conservative) or hyperbolic Arps with b-factor and terminal decline (the industry standard for shale)
- **Decline-rate calculators** — derive your decline rate from IP vs. 12-month production or from a production-history regression
- **NPV analysis** — discounts the full royalty stream at an adjustable rate (end-of-year convention, disclosed)
- **State severance tax auto-fill** — 12 states with per-commodity rates, including Oklahoma's tiered 5%/7% new-well structure (HB 1010xx)
- **Sensitivity analysis table** — price vs. royalty rate matrix with color-coded cells; highlights your current inputs
- **Production decline schedule** — year-by-year projection with a bar chart
- **Mineral value estimator** — 3×–5× annual income range, in total dollars and $/NMA
- **Lease bonus comparison** — enter a $/NMA offer and see how it stacks up against projected royalty income, with a plain-English verdict
- **Demo presets** — one-click example scenarios (inherited STACK minerals, SCOOP co-development, Anadarko gas, legacy vertical)
- **Share URL** — encodes all inputs (including the bonus offer) as query parameters so any scenario can be shared or bookmarked
- **Scenario persistence** — your inputs are saved locally in your browser and restored on your next visit
- **Print / Save PDF** — two report layouts: a one-page summary and a two-page full report
- **Accessible & mobile responsive** — aria-live results, keyboard-operable tooltips and dialogs, WCAG AA contrast, reduced-motion support

## Inputs

| Field | Description |
|---|---|
| Gross Acres | Total spacing unit acreage (chips for common unit sizes) |
| Net Mineral Acres (NMA) | Your actual owned mineral acres after fractional interests |
| Royalty Rate | 1/8, 1/6, 3/16, 1/5, 1/4, or custom % |
| Number of Wells | Producing wells on your acreage (1–20) |
| Commodity | Oil ($/BBL) or Gas ($/MCF) |
| Production per Well | Initial daily rate (BOE/day or MCF/day) |
| Decline Rate | Annual year-1 decline (0–80%), manual or calculated |
| Decline Model | Exponential, or hyperbolic (Arps) with b-factor and terminal rate |
| Years to Model | Projection horizon (1–30 years) |
| Discount Rate | For NPV (5–25%) |
| Commodity Price | Base price with Bear/Base/Bull/Custom scenario |
| Producing State | Auto-fills severance tax per state and commodity; Oklahoma models the 5%→7% new-well tiering |
| Severance Tax | State production tax (auto-filled, editable via Custom) |
| Ad Valorem / Other Tax | Other production-value taxes (OK default is the 0.095% petroleum excise tax — OK's GPT is levied in lieu of ad valorem) |
| Post-Production Deductions | Optional gathering/compression/processing haircut (1–30%) |

## Core Formula

```
Royalty Decimal  = (NMA ÷ Gross Acres) × Royalty Rate

Annual Royalty   = (Production/day × Wells × 365) × Price × Royalty Decimal × (1 − Tax Rate)

Monthly Royalty  = Annual ÷ 12

Exponential:  Year N Production = IP × (1 − Annual Decline)^(N−1)
Hyperbolic:   q(t) = IP ÷ (1 + b·Di·t)^(1/b), switching to exponential at the terminal rate
```

All figures use 365 days/year consistently across the live results panel, sensitivity table, and decline schedule.

## Deployment

The calculator is a single self-contained `index.html` with no build step and no runtime API calls (Google Fonts is the only external request). Alongside it the repo ships:

| Path | Purpose |
|---|---|
| `vendor/pdf.min.js`, `vendor/pdf.worker.min.js` | pdf.js, vendored so the Lease Analyzer works with no CDN dependency and no upload |
| `vendor/tesseract/` | tesseract.js + LSTM cores + English data (~10 MB), fetched by the browser only when a scanned PDF is opened |
| `data/ok/` | Per-county Oklahoma well data — `index.json` plus one file per county, fetched on demand |
| `tools/build_occ_data.py` | Rebuilds `data/ok/` from the OCC's published files |
| `legal.html`, `404.html`, `robots.txt`, `sitemap.xml`, `CNAME` | Site support and SEO |

### Refreshing the Oklahoma well data

The OCC serves no CORS headers, so a static page cannot query it live. Instead the data is a derived snapshot, rebuilt with:

```bash
python tools/build_occ_data.py
```

That downloads the OCC's daily-refreshed [completions and intent-to-drill workbooks](https://oklahoma.gov/occ/divisions/oil-gas/oil-gas-data.html) (~230 MB, cached in `tools/.occ-cache/` and gitignored), keeps completions since 2010 that report an initial test rate, and writes one compact JSON per county. Commit the regenerated `data/ok/` to publish it. Re-running monthly keeps the lookup current; the build date is shown to users in the lookup panel.

## Limitations

This tool produces estimates, not guarantees. Actual royalty income depends on factors not modeled here — actual decline behavior, commodity price volatility, lease-specific terms (including post-production deduction language), and title complexity.

The **Lease Analyzer** matches common lease language patterns; it does not read a document the way a lawyer does. It can miss non-standard drafting, handwritten riders, and exhibits, and a clause it fails to flag may still be present. Every finding shows the text it matched so you can verify it.

Text recovered by OCR from a scan will contain misread characters; the analyzer says so when it used OCR, and every finding quotes the text it matched so you can check it against the document.

Its rules are deliberately asymmetric: a false "in your favor" is far more damaging than a false warning, so a provision is only graded favorable on specific, explicit language. Cost-free royalty language, for example, only counts when it is tied to the royalty *and* reaches post-production costs — "free of the costs of drilling and development" is boilerplate the operator bears anyway, and the Producers 88 form's "free of cost to lessor" describes oil delivered in kind, not deductions. Where a lease contains both cost-free language and wellhead valuation, the analyzer reports the conflict rather than picking a winner. Leased tract acreage is never written into the calculator's Gross Acres, because the spacing unit is a different number.

The **well lookup** reports operator-submitted initial test rates from OCC Form 1002A — a well's best day, not its average — and reflects a snapshot of OCC records, not live status.

**Not financial or legal advice.** Consult a licensed landman, petroleum engineer, or O&G attorney before making lease or investment decisions.

## Built By

Alex Gerardi — Energy Management, University of Oklahoma

[Portfolio](https://aogerardi.github.io/) · [LinkedIn](https://www.linkedin.com/in/alex-gerardi/) · alexogerardi@gmail.com

Built with [Claude Code](https://claude.com/claude-code)
