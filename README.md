# Benefits Cliff Estimator

A free, browser-based calculator that estimates the real take-home value of a job offer for people receiving disability-related and means-tested benefits. It models how SSDI, SSI, SNAP, HUD/Section 8, Medicaid, Medicare premium help (QMB/MSP), EITC, WIC, CCDF child care, LIHEAP, and Lifeline interact with earned income — and surfaces "benefit cliffs" where each extra dollar earned costs more than a dollar in lost support.

**Live site:** [https://mlpage910.github.io/benefits-cliff-estimator/](https://mlpage910.github.io/benefits-cliff-estimator/)

---

## What it does

The tool answers one question: **if I take this job, how much of the wage actually reaches me each month after taxes and benefit interactions?**

It outputs three scenarios on the same offer:

- **Best case** — every benefit retained at its model-projected level
- **Middle case** — QMB/MSP (Medicare premium help) lost, HUD retained
- **Worst case** — QMB/MSP and HUD both lost; full Fair Market Rent exposure

Each scenario shows a monthly dollar figure and an effective hourly rate, plus a line-by-line breakdown of every deduction.

### Modeled programs

| Program                                | What's modeled                                                                                                                                                 | Primary source                                                                                                              |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Federal income tax (2026)              | Bracketed by filing status, standard deduction, IRWE deduction for disabled workers                                                                            | [IRS Rev. Proc. 2025-32](https://www.irs.gov/newsroom/irs-releases-tax-inflation-adjustments-for-tax-year-2026)             |
| FICA                                   | 7.65% (SS 6.2% to $184,500 + Medicare 1.45%)                                                                                                                   | [IRS Topic 751](https://www.irs.gov/taxtopics/tc751)                                                                        |
| State income tax                       | Per-state effective rate (53-state dataset)                                                                                                                    | State revenue departments                                                                                                   |
| SSDI                                   | TWP ($1,210/mo), SGA ($1,690 non-blind / $2,830 blind), 36-month EPE cash suspension                                                                           | [SSA 2026 COLA fact sheet](https://www.ssa.gov/news/en/cola/factsheets/2026.html)                                           |
| SSI                                    | $1 reduction per $2 earned above $85, 2026 federal benefit rate $967/mo                                                                                        | [SSA SSI 2026](https://www.ssa.gov/news/en/cola/factsheets/2026.html)                                                       |
| Section 1619(b)                        | Per-state earned-income ceilings preserve Medicaid after SSI cash ends                                                                                         | [SSA Red Book](https://www.ssa.gov/redbook/)                                                                                |
| Medicaid                               | ACA expansion (138% FPL), MAGI rules, Medicaid Buy-In (working-disabled) where available, ACA Marketplace premium estimate when none qualifies                 | [HHS / CMS Medicaid](https://www.medicaid.gov/), [Healthcare.gov](https://www.healthcare.gov/)                              |
| Medicare 24-month wait                 | SSDI beneficiaries wait 24 months from entitlement; ALS and ESRD waive the wait                                                                                | 42 U.S.C. § 426                                                                                                             |
| QMB / MSP                              | 2026 Part B premium exposure $202.90/mo if lost                                                                                                                | [CMS 2026 Part B premium](https://www.cms.gov/)                                                                             |
| SNAP                                   | 24% earnings-reduction floor, **capped at state-specific USDA FY2026 max allotment**, with **hard cliff at state BBCE gross-income limit** (130%–200% FPL)     | [USDA FNS COLA FY2026](https://www.fns.usda.gov/snap/allotment/cola), [USDA BBCE chart](https://www.fns.usda.gov/snap/broad-based-categorical-eligibility) |
| HUD / Section 8                        | 30% tenant contribution rule (retained) vs. full FY2026 1-BR Fair Market Rent exposure (lost)                                                                  | [HUD FY2026 FMR workbook](https://www.huduser.gov/portal/datasets/fmr/fmr2026/FY26_FMRs.xlsx)                               |
| EITC                                   | 2026 phase-in / plateau / phase-out by filing status and qualifying-child count                                                                                | [IRS EITC 2026](https://www.irs.gov/credits-deductions/individuals/earned-income-tax-credit-eitc)                           |
| WIC                                    | 185% FPL cliff for households with a pregnant member, postpartum parent, or child under 5                                                                      | [USDA FNS WIC](https://www.fns.usda.gov/wic)                                                                                |
| CCDF (child care subsidy)              | 85% State Median Income cliff                                                                                                                                  | [HHS ACF CCDF](https://www.acf.hhs.gov/occ)                                                                                 |
| LIHEAP                                 | Federal floor: greater of 150% FPL or 60% SMI; categorical via SSI/SNAP/TANF preserves access                                                                  | [HHS ACF LIHEAP](https://www.acf.hhs.gov/ocs/programs/liheap)                                                               |
| Lifeline                               | 135% FPL or categorical (SSI/SNAP/Medicaid/FPHA/VA pension); benefit $9.25/mo non-Tribal                                                                       | [USAC Lifeline](https://www.usac.org/lifeline/consumer-eligibility/)                                                        |

### SNAP state-specific accuracy (FY2026)

The SNAP loss calculation has two hard caps on top of the 24% earnings-reduction floor:

- **Allotment cap** — you can never lose more than the USDA FY2026 maximum monthly allotment for your state and household size. The tool ships with full tables for the 48 contiguous states + DC, Alaska (Urban / Rural 1 / Rural 2), Hawaii, Guam, and the Virgin Islands, plus the per-additional-member add-on for households of 9+.
- **Gross-income cliff** — when combined gross monthly income exceeds your state's SNAP gross-income limit, the entire allotment is lost (full cliff). The federal floor is 130% FPL; the 43 BBCE states + DC use a higher limit, up to 200% FPL. Connecticut, California, Pennsylvania, Washington, and others are at 200%. Arizona, New Jersey, Rhode Island, and Vermont are at 185%. Illinois and Texas are at 165%. Kansas, Mississippi, Missouri, Ohio, Oklahoma, South Carolina, South Dakota, Tennessee, Utah, and Wyoming stay at the 130% federal floor.

When SNAP is checked, the calculator shows your state's exact max and gross-income limit live under the checkbox. When the cliff is hit, a red "SNAP cliff (XX% FPL)" pill appears in the headline and the loss row is relabeled "SNAP allotment lost (income cliff)."

### Two estimation modes

- **Quick estimate (default)** — uses national averages (SSI $967/mo, SNAP 24% formula with household-size adjustment, HUD $489 baseline tenant rent). Fast, ballpark numbers.
- **Use my exact amounts** — enter the actual monthly dollar amounts from your benefit award letters and rent statement. The tool uses your figures as caps so the model never over- or under-states your loss.

### What it does *not* model

- Withholding amounts on a paycheck (the output is annual liability ÷ 12, not W-4 withholding)
- State-funded SNAP supplements (California CalFresh minimum, New York elderly/disabled add-ons) — the federal max is used as the cap, which under-states rather than over-states what you could lose
- TANF cash assistance
- State-level Medicaid Buy-In premium tiers below the federal Working Disabled threshold
- Imputed income from employer benefits (health insurance, retirement matches)

---

## How to use the calculator

1. Pick your **state** and enter the **job offer** (wage and pay frequency).
2. Set your **filing status** and **household size**.
3. Check the benefits you currently receive. Each checkbox shows what the tool will model for that program.
4. If you want a more accurate estimate, switch to **"Use my exact amounts"** and enter the dollar amounts from your award letters and rent statement.
5. Read the three result cards (best / middle / worst) and the monthly breakdown.
6. The **Benefit-cliff gap** section compares your take-home to a non-disabled worker with the same paycheck — that gap is what the benefit system removes from a disabled worker for the same hours.

**Important:** This is an estimate. Real benefit changes depend on individualized SSA review, your state's specific deductions and resource limits, your exact shelter and medical expenses, and timing. Always consult a [Ticket to Work WIPA](https://choosework.ssa.gov/findhelp/) benefits counselor before changing work or benefit status.

---

## How to make / update it

The site is a static React + Vite + Tailwind bundle. The whole calculation engine runs in the browser — there is no backend, no database, no user data leaves the device.

### Repo layout

```
benefits-calc/
├── src/
│   ├── App.jsx                  ← UI, state, layout
│   ├── Methodology.jsx          ← Methodology page (citations live here)
│   ├── engine.js                ← Tax + benefit math (entry point: estimate())
│   ├── snap_data.js             ← USDA FY2026 SNAP allotments + BBCE table
│   ├── medicaid_data.js         ← Medicaid pathways + ACA premium estimate
│   ├── ticket_to_work_data.js   ← 1619(b) thresholds, Medicare wait, TTW
│   ├── eitc_data.js             ← EITC + WIC + CCDF + LIHEAP + Lifeline
│   ├── states.json              ← Per-state min wage, FMR, effective tax rate
│   ├── index.css                ← Tailwind base + design tokens
│   └── main.jsx                 ← React entry
├── dist/                        ← Build output (this is what GitHub Pages serves)
├── index.html                   ← Vite HTML shell
├── package.json
├── tailwind.config.js
└── vite.config.js
```

### Local development

```bash
git clone https://github.com/mlpage910/benefits-cliff-estimator.git
cd benefits-cliff-estimator
npm install
npm run dev          # http://localhost:5173 with hot reload
```

### Build for production

```bash
npm run build        # outputs to dist/
npm run preview      # http://localhost:4173, serves the production bundle
```

The build is a single HTML file plus two assets (one JS, one CSS). The JS bundle is ~240 KB minified, ~73 KB gzipped.

### Deploy to GitHub Pages

The repo is configured with `base: './'` in `vite.config.js` so the bundle works from any subdirectory. To publish:

1. Run `npm run build`.
2. Copy or replace the contents of `dist/` in the repo root branch GitHub Pages serves from (typically `main` or `gh-pages`). The simplest setup is to commit `dist/` directly and point Pages at `/dist`.
3. Push to GitHub. Pages picks up the change within a minute or two.

If you'd rather not commit `dist/`, set up a GitHub Action that runs `npm run build` on push and deploys the output — GitHub's "Deploy static content to Pages" template works out of the box.

### Updating numbers each year

The tool ships with FY2026 / TY2026 numbers. To roll forward to the next year:

| Constant                          | Where                             | Source to check                                                                |
| --------------------------------- | --------------------------------- | ------------------------------------------------------------------------------ |
| Federal tax brackets, std ded.    | `engine.js` → `FED_BRACKETS_*`    | IRS annual Rev. Proc.                                                          |
| SS wage base, Part B premium      | `engine.js` → `SS_WAGE_BASE`, `PART_B_PREMIUM_*` | SSA COLA fact sheet, CMS Part B premium release             |
| TWP, SGA, blind SGA, SSI FBR      | `engine.js` → `TWP_*`, `SGA_*`, `AVG_SSDI_BENEFIT_*` | SSA COLA fact sheet                                       |
| SNAP max allotments               | `snap_data.js` → `SNAP_MAX_ALLOTMENT_*` | [USDA FNS COLA](https://www.fns.usda.gov/snap/allotment/cola) (each Aug)  |
| SNAP gross-income limits          | `snap_data.js` → `SNAP_GROSS_INCOME_LIMIT_*` | Same USDA COLA memo                                                  |
| SNAP BBCE state list              | `snap_data.js` → `SNAP_BBCE_GROSS_INCOME_PCT` | [USDA BBCE chart](https://www.fns.usda.gov/snap/broad-based-categorical-eligibility) — verify when states change their TANF rules |
| 1-BR Fair Market Rent             | `states.json` → `fmr1br`          | [HUD User FMR datasets](https://www.huduser.gov/portal/datasets/fmr.html)      |
| EITC parameters                   | `eitc_data.js`                    | IRS EITC tables                                                                |
| 1619(b) state thresholds          | `ticket_to_work_data.js`          | SSA POMS SI 02302.200                                                          |
| Federal poverty guidelines        | Used in FPL math across files     | [HHS ASPE Poverty Guidelines](https://aspe.hhs.gov/topics/poverty-economic-mobility/poverty-guidelines) |

After updating, run `npm run build` and redeploy.

### Short-code edit on GitHub.com

If you only need a tiny change (a typo, a citation URL), you can edit a file directly in the GitHub web UI:

1. Open the file (e.g. `src/Methodology.jsx`) in the repo.
2. Click the pencil icon (top right of the file view).
3. Make the edit and commit.
4. Either commit `dist/` rebuilt locally afterward, or use a build action so Pages picks it up automatically.

Edits to `src/*` only take effect after a rebuild — the live site serves the contents of `dist/`, not `src/`.

---

## Methodology and citations

All thresholds, formulas, and constants come from primary government / regulatory sources. The in-app Methodology page (linked in the site header) has the full citation list with direct URLs to USDA FNS, SSA, IRS, HUD User, HHS ACF, CMS, USAC, and the federal poverty guidelines. No conclusions or numbers are drawn from unpublished research or third-party summaries.

---

## License

This project is released for educational and personal-use purposes. The calculation logic is published openly so benefits counselors, advocates, and people navigating work-incentive decisions can audit, fork, or adapt it.

---

## Disclaimer

This tool is an estimator. It uses simplified, generalized assumptions and does not reflect every household's specific situation. Actual benefit changes depend on individualized SSA review, state-specific deductions, household composition, shelter costs, medical expenses, IRWE, resources, and timing. Always consult a Ticket to Work WIPA or a SHIP counselor before making changes to your work status or benefit applications.
