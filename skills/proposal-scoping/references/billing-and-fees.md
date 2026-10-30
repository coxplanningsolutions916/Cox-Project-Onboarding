# Billing, Rates, and Fee Backup

## Canonical billable rates (MSRP)

Exact role names — no abbreviations or variations. Analyst tiers match planner tiers (Chris, 2026-10-10).

| Role | MSRP |
|---|---|
| Principal Biologist/Planner/Engineer | $250/hr |
| Senior Biologist | $175/hr |
| Senior Planner | $175/hr |
| Senior Engineer | $190/hr |
| Biologist | $150/hr |
| Planner | $150/hr |
| Engineer | $165/hr |
| Associate Planner | $125/hr |
| GIS Analyst/CAD Designer | $150/hr |
| Senior Analyst | $175/hr |
| Analyst | $150/hr |
| Associate Analyst | $125/hr |
| Project Coordinator | $125/hr |

Market adjustments (e.g., Florida ~15.5% below California) and the 20% Preferred Partner discount apply only when Chris directs. Reimbursables (Cox MSA v2.0 Section 4.5 and Task Order Section 3.2, effective October 9, 2026): subconsultant services at cost +20%; laboratory, printing and other reimbursable costs at cost +15%; mileage at the current IRS rate. A client still on the earlier combined agreement (Article 4.6, everything at cost +15%; Cox has always billed reimbursables, so never say otherwise) moves to these rates by signing their next task order, which carries the MSA v2.0 as Part B (`msa_for`, Chris 12 Oct 2026).

## The cost floor is not MSRP

MSRP is the client-facing billable value. The **internal cost floor** is what the work actually costs Cox to produce, and Claude-assisted work compresses that materially. Price for real margin against the cost floor, not against MSRP rate math.

Use the real cash-cost basis when known. Example from the screening-tool build: the GIS Analyst's nonbillable cost basis is $40/hr, not the $150/hr MSRP — so a 10–20 hour build costs ~$400–$800 cash, against an MSRP-equivalent of $1,500–$3,000. Show both views in the fee backup so the margin decision is explicit, and let Chris set the margin.

## Three billing types

**Fixed Fee (default).** Set per deliverable in advance; internal hours tracked but not billed. Requires proposed fee ≥ estimated cost. Payment terms follow the size of the job (below). Use for defined-deliverable scopes.

## Payment terms (Chris, 2026-10-25; replaces the 40/40/20 default)

Measure the job by its **strategy and documentation** fees (studies, reports, plans, applications Cox prepares), not counting application processing.

- **Small jobs — strategy and documentation under $20,000.**
  - Documentation: **50 percent on signing, 50 percent when the drafts go to the client** (not on final or approval).
  - Application processing (submittal, agency coordination, comment responses, hearings, inspections): a **processing retainer** in the task order, **billed monthly by percent complete against the retainer**, never held until approval. The task order names the retainer amount and says unused retainer is not billed.
- **Larger jobs — strategy and documentation of $20,000 or more.**
  - A **down payment equal to the estimated first month of effort** (month-one hours by role × rate from the schedule, rounded), due on signing and credited against the first monthly invoice(s).
  - Then **monthly by percent complete** on each deliverable, processing included.
- Apply any credit the Step 1 ladder gives against the first payment (a Site Walk credited in full toward 2A Field Verification if signed within 90 days; a legacy findings deposit collected before 2026-10-30). T&M NTE, subscription and public-agency monthly terms are unchanged.
- **Quick, small jobs bill 100 percent up front** (Chris, 2026-10-25): a fixed fee under $5,000 delivered within about three weeks (foot-in-the-door offers: clearance letters, a single permit memo; the Step 1 planner levels are always paid on order) is due in full on signing, **unless the client has an established track record** (see Payment standing), in which case it bills on delivery.
- **Security deposits for clients flagged "Deposit required"** (see Payment standing): in addition to the terms above, a deposit equal to about one month of expected billing (at least $1,000; 10 percent of the contract for jobs over $50,000), held and applied to the final invoice, not to the first one. Name it in the task order's billing paragraph as a security deposit.

### Payment standing (who pays up front or a deposit)

Each client carries a Payment standing: **Established**, **New** or **Deposit required**.
- **Established:** at least two invoices paid within terms in the last 12 months and nothing more than 60 days past due. Standard terms; no up-front or deposit rules.
- **New:** no payment history with Cox. Quick small jobs are paid up front; other terms as above.
- **Deposit required:** any invoice more than 60 days past due in the last 12 months, a prior collections or write-off, or an engagement with elevated dispute risk (after-the-fact or enforcement work, an owner-builder or single-asset entity on a job of $20,000 or more, a client who has disputed a scope or fee). Chris can set or clear it by hand with a reason.
Check the standing before writing the billing paragraph: call the `payment_standing` tool (client = the project key, company name or Productive company id; fee = the strategy and documentation fee; quick = true for a job delivered within about three weeks). It returns the standing, its reasons, whether Chris set it by hand, and the terms computed from this rule (up front or not, the security deposit amount, a schedule sentence). The dashboard recomputes the standing nightly from Productive and QuickBooks. If it comes back unknown, ask Chris.
- After drafting, read `terms_check` in `draft_proposal`'s result: advisory notes where the drafted billing, milestones or payment don't match this rule (a quick job for a New client not 100 percent on signing; a Deposit required client with no security deposit line; a small job without the 50/50 split; a larger job without the first-month down payment). Fix the draft or tell Chris why it differs. It never blocks the draft.
- Worked example: 2437-california Task Order 2 ($13,100 documentation → 50/50; $5,400 processing retainer → monthly percent complete).

**T&M NTE.** Billed monthly on actual hours to a not-to-exceed ceiling; 30-day terms, 1% monthly late fee, ~10% contingency held in reserve. Use for agency-driven, iterative, multi-year, or public-agency work where fixed fees are hard to defend.

**Subscription / Productized (newest type).** For a reusable tool or data product sold to many clients (e.g., the screening tool). Priced as a recurring monthly or annual fee, not a one-time build. Design principles:
- Price **low to attract, not so low it fails to gate** — the price is a qualification filter, trivial against a development deal but high enough to signal value and screen out tire-kickers.
- The subscription is a **marketing/funnel engine**, not the margin center. It runs near cost-plus-modest-margin; the real revenue is the downstream paid work it qualifies leads into (ISRs, Step 2, entitlement engagements).
- Offer a **free trial** (time- or lookup-limited, single jurisdiction) that converts to paid.
- Annual pricing improves cash flow and commitment; offer it at a discount to monthly.
- Anchor/founding clients may get a locked founding rate in exchange for being a reference or case study.
- Billing infrastructure: recurring billing now runs in **Productive** — the sales deal's services become the project budget at onboarding, and anchor clients are invoiced annually from Productive. Do not let infra concerns block the deal.
- Subscription is a candidate Operations Manual addition to the Billing Types section; flag for the Pending Updates queue.

## Step 1 pricing (Q1 Screening)

Step 1 is priced by the model's pricing feed (`canon/pilot.yaml`), not here and not in a task order; `roadmap-scoping` has the ladder and `references/scale-test-and-pricing.md` there the detail. As of 2026-10-30: the membership (Starter $49, Pro $149, Team $399 a month; 30-day trial; the automated Screen 1A and Scenarios read 1B come from its allowances), and the planner levels 1C: Planner Review $500 ($400 for members), credited up to $500 into a Strategy Session; Strategy Session $1,000 standard or $2,500 complex by the scale test; Site Walk $1,000 local or $2,500 remote or large plus expenses, credited toward 2A Field Verification if signed within 90 days. The old 1a / 1b tiers (the $2,500 / $1,000 Initial Screening Report, the screening-tool subscription, the findings deposit) are retired; do not quote them. Desktop analysis beyond a Strategy Session is scoped as task order deliverables at Q2 and later.

## Hours benchmarks (reference ranges)

| Task type | Range | Primary role |
|---|---|---|
| Project setup (one-time) | 4 hrs | Principal |
| Desktop research (per topic) | 1–4 hrs | Planner |
| GIS base map / map app setup (per jurisdiction) | 1–2 hrs base; 10–20 hrs for a full screening map app | GIS Analyst/CAD Designer |
| Prepare report content | 16–40 hrs | Planner |
| Review report content | 2–12 hrs | Principal/Senior |
| Prepare project description | 8–16 hrs | Planner |
| CEQA coordination | 4–8 hrs | Planner |
| Prepare permit application | 8 hrs | Planner |
| Respond to agency comments | 8–16 hrs | Principal |
| Biological field survey (per day) | 6–8 hrs | Biologist |
| SWPPP preparation | 8–16 hrs | Planner/Senior |

## What Cox guarantees

Range-based estimates grounded in experience, and correctly executed planning, regulatory, design, and environmental work — agency standards, code compliance, internal QA. **Not** permit approvals; **not** valuation outcomes. State this in every proposal.
