# Estimating Basis — per-deliverable hour ranges from real Cox budgets

This is the hour reference for **step C (budget and fee backup)** and for change orders. It replaces
guessing from the coarse "Hours benchmarks" table in `billing-and-fees.md` with **empirical ranges
rolled up from every Productive project budget**. The Asana phase-template task chains badly
under-state the heavy analytical deliverables (template BRA 9h vs. real median ~68h; template §1602
16h vs. ~68h) — do **not** estimate from template chains; estimate from this.

## How to read it

- **Median** = the reference point. Start here. (Means are skewed by big outliers — use the median.)
- **Range (min–max)** = the judgment band. The spread is real: BRA has run 14h to 337h depending on
  parcel size, species count, and agency. Place the project inside the band deliberately.
- **n / confidence** — `calibrated` (n≥3) is trustworthy; `provisional` (n=2) is a hint; `single`
  (n=1) is one anchor, sanity-check it. Full long-tail (124 deliverables, incl. single-anchor and
  permit variants like Emergency / Nationwide / Letter-of-Permission) lives in
  `cox-productive-tools/estimating_basis.json`; regenerate with `estimating_basis.py --json`.
- **Always adjust to the specific job** — this is a starting point grounded in history, not a quote.
  Then apply the rate table, the cost-floor discipline, subs at +15%, and **8% PM** (go-forward).

## Calibrated deliverables (n≥3)

| Deliverable | Phase | Median h | Range | n | Primary role |
|---|---|--:|--:|--:|---|
| Biological Assessment (BA) | 01 | 150 | 52–350 | 6 | Planner / Principal |
| Biological Resources Assessment (BRA) | 01 | 68 | 14–337 | 10 | Planner / Principal |
| Cultural & Tribal Cultural Resources Assessment | 01 | 47 | 10–131 | 10 | Planner / subK |
| Wetland Delineation | 01 | 43 | 29–114 | 7 | Biologist / Planner |
| Special-Status Plant Surveys | 01 | 24 | 15–81 | 4 | Biologist |
| Permit / Feasibility Analysis | 02 | 12 | 5–90 | 6 | Planner |
| Impacts Maps | 02 | 41 | 25–79 | 3 | CAD / GIS |
| Conceptual Landscape Plan | 02 | 49 | 31–147 | 5 | CAD / Planner |
| Project Description (per doc; fed+state combined runs higher) | 03 | 99 | 24–279 | 7 | Planner / Principal |
| Alternatives Analysis (LEDPA) | 03 | 67 | 8–89 | 8 | Planner / Principal |
| Mitigation Plan | 03 | 56 | 20–268 | 7 | Planner / Principal |
| NEPA Environmental Assessment (EA) | 03 | 152 | 119–162 | 3 | Planner / Principal |
| Landscape Restoration Plan | 03 | 66 | 11–91 | 3 | Planner / CAD |
| CWA §404 Permit (USACE) | 04 | 84 | 69–108 | 4 | Planner / Senior |
| CWA §401 WQC / WDR (RWQCB) | 04 | 80 | 24–104 | 9 | Planner / Senior |
| CDFW §1602 LSA Agreement | 04 | 68 | 24–93 | 7 | Planner / Senior |
| Pre-Construction Surveys & WEAP | 05 | 37 | 6–84 | 7 | Biologist / Planner |
| Construction Monitoring & Field Compliance | 05 | 31 | 14–250 | 4 | Surveyor / Planner |
| Post-Construction Compliance Reporting | 05 | 38 | 7–65 | 4 | Planner |
| Post-Project Monitoring & Mitigation Implementation | 05 | 21 | 10–148 | 3 | Planner / Surveyor |
| Vernal Pool / Branchiopod Surveys | 05 | 35 | 11–44 | 3 | Biologist |

Provisional / single anchors worth knowing (sanity-check before use): CEQA Initial Study ~18h (n2);
Jurisdictional Wetland Determination ~45h; USFS / Public Lands Use Permit ~93h; FESA §10 ITP ~128h;
CVFPB Encroachment ~84h. Emergency / Nationwide / Letter-of-Permission variants run materially
different from the standard permit — see the JSON.

## New proposal vs. change order

- **New proposal (Mode 1, e.g. Napa 55):** estimate from the medians, placed in the band for the
  parcel. **No actuals** exist — and for Napa 55 specifically, its Harvest history is Barnett legacy
  and must never be pulled. Build the fee backup, get Chris's margin approval, then write services.
- **Change order (Mode 2):** first **ground in actuals**. Run
  `cox-productive-tools/co_actuals.py <project_id> --rate <blended>` to get approved-budget vs.
  worked hours per deliverable. Separate *already-incurred overage* (worked > budget — sunk revision
  cost) from *forward work* (not-started deliverables — genuinely incremental). The CO estimate =
  the incremental hours needed **going forward**, informed by the overage but not equal to it. Then
  Chris + Kristin layer their anticipated additional hours per line before pricing.
