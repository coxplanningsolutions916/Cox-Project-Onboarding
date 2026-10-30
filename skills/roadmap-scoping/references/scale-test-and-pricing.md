# Step 1 pricing and the scale test (as of 2026-10-30)

Source: the Cox Entitlement Model's pricing feed, `canon/pilot.yaml` (membership, trial, planner_levels, billing, turnaround_business_days), phrased by `engine/offers.py` and published to the portal as `roadmaps/_pricing.json`; the scale test is `engine/review.py` `scale_test()`. The portal's pricing page (`portal/pricing_page.py`) and checkout (`portal/checkout.py`) read the same feed and keep no numbers of their own. Neither should this file: if the feed has moved, the feed wins.

Superseded and retired: Step 1a Screening $500, Roadmap $2,500, Roadmap Plus $5,000 (Sept 30), the 12 Oct per-order levels and Map license (removed 2026-10-20, none ever sold), the Strategy Session at $2,500 / $5,000 (repriced 2026-10-30), the 48-hour delivery promise, and the credited $500 / $1,000 findings deposit.

## Membership (Q1 steps 1A and 1B)

| Tier | Monthly | For | Map coverage | Screens a month | Scenarios reads a month | Users | Planner Review |
|------|---------|-----|--------------|-----------------|-------------------------|-------|----------------|
| Starter | $49 | Owners and investors watching a few sites | Home county | 5 | 1 | 1 | $400 |
| Pro (most popular) | $149 | Builders, brokers and active investors | A growth region | 25 | 5 | 2 | $400 |
| Team | $399 | Developers working several regions | All of California | 100 | 20 | 5 | $400, priority scheduling |

- Annual: a year for ten months' price.
- Screen pack: 25 more Screens for $150.
- Pro is free while a Cox task order is active.
- Pro and Team add batch address upload and weekly recommended parcels for each saved project type; Starter has 10 saved projects and one project type.
- Trial: 30 days, 3 Screens, 1 Scenarios read, no card. At the end of each 30 days the member can complete an evaluation form, which unlocks another 30 days from completion, up to 2 evaluations (90 days at most).
- On sale: decided, but published as on sale only once card billing (Stripe) is live. As of 2026-10-30 it is not.
- Sales tax on memberships and the Screen pack from 1 Jan 2027; planner levels and task orders are services and exempt.

The **Screen (1A)** and the **Scenarios read (1B)** are automated, labeled Unreviewed, and come from the monthly allowance. A Screen is delivered in 2 business days. The Scenarios read needs a Screen of the same parcel; its outlook is a band only; its budget appears only where the fee engine has published or benchmarked inputs, otherwise "Budget pending" with what it needs. Every read ends with "What would settle it", led by the Planner Review.

## Planner levels (Q1 step 1C, sold per order)

| Level | Price | Turnaround | Credit | What it is |
|-------|-------|------------|--------|-----------|
| 1. Planner Review | $500; $400 for members | 2 business days; 1 on Team | Up to $500 off a Strategy Session on the same site | A planner checks the automated Screen and Scenarios read, confirms the facts from up to 5 of the client's documents (100 pages), and signs the Screen |
| 2. Strategy Session | $1,000 standard or $2,500 complex, by the scale test; no member price | 5 business days standard, 10 complex | — | A planner ranks the paths, prices the next studies and walks the client through them in a session; every document on the site is read in full |
| 3. Site Walk | $1,000 local or $2,500 remote or large, plus expenses; no member price | scheduled with the client | Credited in full toward 2A Field Verification if signed within 90 days | A planner walks the site with the client and notes what needs field verification |

"Local" means within about an hour's drive of Sacramento. A completed level's credit is good for 12 months after delivery. Only Level 1 has a member price; the larger levels are not discounted, they are credited forward instead.

How the price reads to a client (engine/offers.py): "Planner Review, $400 for members, $500 otherwise"; "Strategy Session, $1,000 for a standard site or $2,500 for a complex one" (one price once the tier is confirmed); "Site Walk, $1,000 local or $2,500 remote or large, plus expenses."

## The scale test (sets the Strategy Session tier)

The model answers what it can from the screen and the layers; a planner confirms the tier before payment (the portal's checkout raises no invoice until he does). **Any one trigger makes the site complex; no trigger is standard.**

1. **Jurisdictions.** Parcels in more than one jurisdiction.
2. **Sites.** More than one separate site. Two or more adjacent parcels in one ownership count as one site.
3. **Mapped resources.** A positive mapped or field finding of waters, wetlands, flood hazard or listed-species habitat on or adjacent to the site.
4. **Discretionary entitlement.** The approval set needs a rezone, General Plan amendment, specific plan, use permit or conditional use permit, variance, annexation, planned development, tentative or subdivision map, or development agreement.
5. **Board package.** The client needs a package for a board, an investment committee, a lender or a public agency.

The engine still names the outcomes by their old rung keys (`roadmap` = standard, `plus` = complex). A planner may override the tier; an override below the test's result needs a written reason (rule R71) and prints on the report. Edge cases go to Chris. Do not downgrade a complex site to win the sale.

## Internal hours basis

Record any hours basis as `estimated_hours` on the Productive service, never in a name or anything client-facing. Hours behind the Strategy Session (Chris, 2026-10-30; `staff_hours` in `canon/pilot.yaml`): **standard, 4 hours of Chris's time**; **complex, about 10 hours**, Chris alone or Chris with junior staff depending on the project. Preliminary mapping or multi-agency approval requirements add hours; say so in the estimate and budget the extra as junior time where it fits.

## What the client is told about price

"Our membership starts at $49 a month, and you can try it free for 30 days: the map, three Screens and a Scenarios read, no card needed. When a site is worth a planner's time, a Planner Review is $400 for members and $500 otherwise, and it comes off a Strategy Session if you go on to one. A Strategy Session is $1,000 for a standard site or $2,500 for a complex one; we confirm which before you pay. A Site Walk is $1,000 locally or $2,500 for a remote or large site, plus expenses, and it is credited toward field verification if you sign within 90 days."

Check the feed before quoting this paragraph.
