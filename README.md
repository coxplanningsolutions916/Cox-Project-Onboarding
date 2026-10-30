# Cox Proposals

Sales and proposals automation for Cox Planning Solutions, packaged for Claude Cowork. Covers the pipeline from a lead landing through scoping a signed-ready proposal and handling change orders.

## Skills

### new-lead-intake-screen (pre-handoff)
From a lead landing to the findings email: scaffolds the proposals folder, files the transcript and intake documents, screens zoning/General Plan/development standards/environmental constraints and the California regulatory levers, starts the draft Roadmap, and drafts a lead-with-value findings email that offers the next step on the current ladder (the trial, a planner level, or Task Order 1). Trigger: "new lead, [name], [address or APN]."

### proposal-scoping (v0.4.0: writes services to the Productive deal + generates the branded proposal instead of a handoff memo) (Mode 1)
Once a prospect bites: builds the scope of work, the internal fee backup and budget (against the real cost floor, not MSRP), timeline, and payment terms, then writes the scoped deliverables as services on the Productive sales deal and generates the branded client proposal from them. The proposal is drafted on the Cox dashboard (`draft_proposal`); Chris releases and signs it, and the prospect signs from the client portal. Task Order 1 is built from the draft Roadmap's deficiencies; projects start at a signed task order. Payment terms follow job size: documentation under $20K bills 50 percent at signing and 50 percent at drafts, with a processing retainer billed monthly by percent complete; $20K and up takes a first-month down payment, then monthly percent complete; `payment_standing` flags clients who post a security deposit. Handles three billing types — fixed fee, T&M NTE, and subscription/productized. Trigger: "scope a proposal," "fee backup," "build a proposal for [client]."

### roadmap-scoping (Mode 3, v0.20.0)
The Step 1 (Q1 Screening) ladder and every prospect's draft Roadmap. The portal membership (Starter $49, Pro $149, Team $399 a month; a 30-day trial with 3 Screens and a Scenarios read, no card) carries the automated Screen (1A) and the automated Scenarios read (1B). The paid planner levels (1C) sit on top: Planner Review $500 ($400 for members, credited up to $500 into a Strategy Session), Strategy Session $1,000 standard or $2,500 complex by the scale test (a planner confirms the tier before payment), and Site Walk $1,000 local or $2,500 remote or large plus expenses (credited toward 2A Field Verification if signed within 90 days). Covers the scale test, turnaround from the feed, the Automator Roadmap Requested / Delivered stages, portal checkout and QBO pay links, the planner QC gate, and the hand-off to Task Order 1. Every price is read from the model's pricing feed (`canon/pilot.yaml`); the figures here are as of 2026-10-30. Triggers: "roadmap for [address]," "build a roadmap," "strategy session," "planner review," "site walk."

### paid-findings-report
Charges for a call or site visit by selling the matching planner level off the portal, with a QBO pay-link invoice and the record on the Automator card and deal registry. The old $500 / $1,000 credited findings deposit is retired (legacy deposits are still credited at onboarding). Trigger: "paid findings," "site visit fee," "invoice the site walk."

### change-order-scoping (Mode 2)
Modifies an active Task Order: documents only what changed, builds the fee backup for the changed scope, sets a single-milestone payment, and produces the change-order handoff memo. References the existing agreement rather than re-attaching it. Trigger: "change order," "add scope to [project]."

## Composes with
- Upstream: the intake screen and the draft Roadmap feed proposal-scoping; roadmap-scoping sells the Step 1 planner levels in between.
- Downstream: `deal-onboarding` picks up onboarding once a proposal is won (won deal → Productive project built from a native template and trimmed to the deal's services, + budget + drafted down-payment invoice).
- `cox-document-formatting` for any client-facing language.

## Notes
- The firm is "Cox" or "Cox Planning Solutions," never "CPS" — including internal project-number prefixes. Ranges, not point estimates; no approval, timeline or valuation guarantees; no exclamation marks.
- Step 1 prices live in the Cox Entitlement Model's pricing feed (`canon/pilot.yaml`), which the portal also reads. When the feed changes, update roadmap-scoping and its `scale-test-and-pricing.md` reference.
- Regulations come from the model's register first, then official sources only, never mirrors.
- Fee backup, hours, and cost floors stay internal and never appear in client-facing material.
- Subscription/productized pricing is the newest billing type (see proposal-scoping `billing-and-fees.md`); it is a candidate Operations Manual addition to the Billing Types section.
- The regulatory-levers logic is shared with the screening-tool app build — keep both pointed at one canonical source so a legal-reform update fixes both.
