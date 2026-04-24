# MineralCalc — Mineral Rights Royalty Income Calculator

A free, single-file web tool for mineral owners, landmen, and investors to model oil and gas royalty income, run sensitivity analysis, and evaluate lease bonus offers.

**Live site:** [aogerardi.github.io/mineralcalc](https://aogerardi.github.io/mineralcalc)

---

## Features

- **Live royalty income calculator** — monthly, annual, and multi-year cumulative projections update in real time as you type
- **Royalty decimal formula** — shows the exact math behind every number so you can verify and trust the output
- **Sensitivity analysis table** — price vs. royalty rate matrix with color-coded cells; highlights your current inputs
- **Production decline schedule** — year-by-year exponential decline projection with visual income bars
- **Mineral value estimator** — estimates sale value range based on 3×–5× annual income (standard buyer multiple)
- **Lease bonus comparison** — enter a $/NMA offer and see how it stacks up against projected royalty income; includes a plain-English verdict
- **Live price links** — one-click access to current WTI oil and Henry Hub gas benchmarks
- **Share URL** — encodes all inputs as query parameters so any scenario can be shared or bookmarked
- **Print / Save PDF** — clean print stylesheet for paper or PDF export
- **Mobile responsive** — works on phone, tablet, and desktop

## Inputs

| Field | Description |
|---|---|
| Gross Acres | Total spacing unit or tract acreage |
| Net Mineral Acres (NMA) | Your actual owned mineral acres after fractional interests |
| Royalty Rate | 1/8, 3/16, 1/4, 3/8, 1/2, or custom % |
| Number of Wells | Producing wells on your acreage |
| Working Interest % | Optional — if you bear a share of costs |
| Commodity | Oil ($/BBL) or Gas ($/MCF) |
| Production per Well | Initial daily rate (BOE/day or MCF/day) |
| Decline Rate | Annual exponential decline (0–80%) |
| Years to Model | Projection horizon (1–30 years) |
| Commodity Price | Base price with Bear/Base/Bull/Custom scenario |
| Severance Tax | State production tax (Oklahoma default: 7%) |
| Ad Valorem Tax | County property tax on production value |

## Core Formula

```
Royalty Decimal  = (NMA ÷ Gross Acres) × Royalty Rate

Annual Royalty   = (Production/day × Wells × 365) × Price × Royalty Decimal × (1 − Tax Rate)

Monthly Royalty  = Annual ÷ 12

Year N Production = Initial Production × (1 − Annual Decline Rate)^(N−1)
```

All figures use 365 days/year consistently across the live results panel, sensitivity table, and decline schedule.

## Deployment

This is a single `index.html` file with no dependencies, build steps, or external APIs. Deploy anywhere that serves static files.

**GitHub Pages:**
1. Create a new public repository
2. Upload `index.html`
3. Go to Settings → Pages → Branch: main → Save
4. Live at `https://<username>.github.io/<repo-name>/`

## Limitations

This tool produces estimates, not guarantees. Actual royalty income depends on factors not modeled here — post-production cost deductions, actual decline behavior, commodity price volatility, lease-specific terms, and title complexity. **Not financial or legal advice.** Consult a licensed landman, petroleum engineer, or O&G attorney before making lease or investment decisions.

## Built By

Alex Gerardi — Energy Management, University of Oklahoma

[Portfolio](https://aogerardi.github.io/aogerardi/) · [LinkedIn](https://linkedin.com/in/alexogerardi) · alexogerardi@gmail.com

Built with [Claude Code](https://claude.ai/claude-code)
