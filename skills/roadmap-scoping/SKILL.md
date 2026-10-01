---
name: roadmap-scoping
description: "Produce and sell a Cox Planning Solutions Entitlement Roadmap (Mode 3) — the $2,500 front-door product that shows a prospect the whole project before they commit: what to build, how it gets approved, the permit stack, schedule, and order-of-magnitude costs, reviewed by a senior planner and delivered within 48 hours of the request. Also handles the $500 Phase 1A Screening below it (model report plus one staff hour) and the $5,000 Roadmap Plus above it. Use when a prospect asks for a roadmap, when the intake screen is done and the next step is the Roadmap rather than a free findings email, or when a Roadmap request lands from the website, Automator, or a call. Triggers: 'roadmap,' 'entitlement roadmap,' 'build a roadmap for [address],' 'they want the roadmap,' '1A screening,' 'phase 1a,' 'roadmap plus,' 'what would it take to build on [parcel],' 'roadmap requested,' 'deliver the roadmap.' Do NOT use for the free intake screen alone (use new-lead-intake-screen), for a Step 1b ISR or Step 2 proposal (use proposal-scoping), or for a change order (use change-order-scoping)."
---

# Entitlement Roadmap (Mode 3)

The Roadmap is the front door of the Development Decision Ladder. A prospect gives Cox an address; Cox returns, within 48 hours, a short client-facing strategy memo that shows the whole project: what to build, how it gets approved, the permit stack, the schedule, and order-of-magnitude costs with what would firm each range up. It closes with a priced Step 1 or Step 2 proposal. The prospect buys planning judgment applied to their parcel; the screen and the model are how Cox affords to sell that judgment at $2,500.

Decided Sept 30, 2026: one product at one price, sized by scope rather than discount. Full pricing and the scale test in `references/scale-test-and-pricing.md`. The memo structure, proven on Lemon Hill (Sept 15, 2026) and Riego Rd (Sept 21, 2026), is in `references/roadmap-memo-template.md`. The systems sequence (Automator, Productive, QuickBooks, Drive) is in `references/roadmap-setup.md`. The senior planner's review gate is `references/qc-checklist.md`.

## The offer

| Rung | Price | What the client gets | Who buys it |
|------|-------|----------------------|-------------|
| **Phase 1A Screening** | $500 | The model-generated screening report (ten sections: decision, property record, rules and levers, fit, constraints with the biology flags, aerial read, approval set, cost and schedule ranges, assumption register, go or no-go) with one hour of Cox staff review before it goes out. Credited to a Roadmap within 60 days. | Every persona's entry point; the launch campaign's free product |
| **Roadmap** | $2,500 | The 1A report taken to Cox's recommendation with four hours of senior planner time: corrections, strategy and sequencing, the prioritized verification plan, and the working session. Closes with a priced Step 1b or Step 2 proposal. | Developers and investors deciding whether to pursue a parcel |
| **Roadmap Plus** | $5,000 | The Roadmap for a project that fails the scale test: adds a second session, a board-ready summary, and a sub-consultant read (biology, engineering) where needed. | Institutional developers, the ABM list, public agencies |

Below $500 there is nothing to sell: the free intake screen is the lead magnet. The Roadmap price is never discounted; the launch campaign gives away 100 Phase 1A screens. Even the $500 product carries a staff hour, because expert planning staff is what sets Cox apart from automated real-estate analysis apps.

## Inputs to gather

In one pass, not stage by stage:

- **Client name and legal entity**, contact email and phone, and whether the contact is the owner, a buyer, a broker, or an agent (the Roadmap is written to the decision maker).
- **Property address and/or APN**, jurisdiction, approximate acreage, number of parcels.
- **What they want to do** with it, or the decision they face (build, buy, sell entitled, hold).
- **What they already have**: survey, prior studies, a concept plan, prior correspondence with the agency.
- **The scale-test answers** (`references/scale-test-and-pricing.md`): parcels and jurisdictions; mapped waters, wetlands, flood zone, or listed-species habitat; by-right or discretionary.

Proceed with an address alone if that is all there is; flag the gaps in the memo as "what would firm this up."

## Stage 1 — Confirm the request and the rung

1. Find or create the Automator opportunity (`list_opportunities` / `create_opportunity`), confirm the contact is the decision maker, and set `source` to one of the canonical names (Google Ads, LinkedIn, Direct Mail, Referral, Existing Client, Website, BIA Workshop, Podcast, ABM, Meta Ads, Public Agency). Move the opportunity to **Roadmap Requested**.
2. Run the scale test. Record the rung and why in the registry. A 1A Screening can always be upgraded; never downgrade a Plus to fit a budget without Chris.
3. Scaffold `Sales/Proposals/[Lead Name]/` if it does not exist (`new-lead-intake-screen` Stage 1) and create the `Roadmap/` folder under it.
4. Invoice. The Roadmap and Plus are paid on request through a QuickBooks pay-link invoice (sequence in `references/roadmap-setup.md`, section B); the 1A Screening too unless it is one of the 100 campaign screens. Work starts on payment unless Chris says otherwise. State the credit on the invoice note: a 1A Screening credits to a Roadmap within 60 days; a Roadmap credits nothing but closes with a proposal.

## Stage 2 — Screen the property (the model input)

Run the `new-lead-intake-screen` Stage 3 to 5 sequence comprehensively: Acres report and KML filed, zoning and General Plan, development standards, environmental constraints with the live NWI and CARI queries, and the regulatory lever scan. Add, for the Roadmap:

- **Historic aerials.** Review the Google Earth record (ten years or more where available) for site condition, disturbance, ponding in wet-season frames, structures that came and went, and trees to check for nesting. This exhibit was added on Lemon Hill and it is what makes the clean-site base case credible.
- **The agency path.** Identify the discretionary approvals the stated use needs in this jurisdiction (rezone, use permit, design review, subdivision map, density bonus, CEQA pathway), the approval body for each, and whether a federal or state resource permit enters the picture (that is the structural dependency that changes everything; see rule R2 in the QC checklist).
- **Market read, only as far as the memo needs it.** Rents or sale comps enough to say which product form pencils and what the realistic yield is. Cox is not the appraiser; say so.

Everything in the memo traces to this screen. A figure without a source is an assumption and must be labeled as one.

## Stage 3 — Draft the Roadmap

Write the memo to `references/roadmap-memo-template.md`, in Chris's voice per `cox-document-formatting`. The standing rules that matter most:

- **Bottom line first.** Three sentences a client can repeat to a partner: what to build, how it gets approved, what it takes.
- **Ranges, not points, for anything unresolved**, and under each range the one thing that would firm it up. Where the inputs that set a cost do not exist yet, say so and name the drivers instead of inventing a bracket (Riego withdrew its mitigation bracket on exactly this reasoning).
- **Mapped is not field-confirmed.** Every constraint statement says which it is.
- **No hours, rates, roles, margin, or vendor names** in the client document. The internal fee backup lives in Productive and the registry, never in the memo.
- **The non-guarantee**, verbatim from the Terms section of the template.
- **Next step is a proposal**, named: usually the Step 1b ISR or the Steps 2 and 3 package, with the Roadmap's assumptions as the things that work resolves.

Until the Cox Entitlement Program Model (the Claude Code engine in the Sept 25, 2026 build brief) ships, the memo is drafted here from the screen. When the engine ships, this stage calls it for the schedule, fee ranges, and the confidence composition, and the drafting rules above do not change.

## Stage 4 — Senior planner review and the session

The 2-hour senior planner review is what the client is paying for. Work through `references/qc-checklist.md` with Chris or Kristin before anything goes to the client; the checklist is the gate, not a suggestion. Then hold the working session with the client (the Roadmap's four staff hours include it; Plus includes two sessions), walk the memo, capture what they said about their timeline, capital, and appetite, and revise the memo once if the session changed the recommendation.

Delivery target: **memo sent within 48 hours of the request** (24 hours for a Phase 1A Screening). If the screen turns up something that needs field confirmation before the memo can stand behind a recommendation, say so in the memo and keep the clock; the Roadmap is a desktop product.

## Stage 5 — Deliver, record, and convert

1. Save the memo as PDF in `Sales/Proposals/[Lead Name]/Roadmap/` with the Cox cover and footer, and the historic-aerial exhibit and graphic schedule as figures.
2. Send with a short cover email in Chris's voice that names the next step and the proposal that follows.
3. Move the Automator opportunity to **Roadmap Delivered**, set the value to the Roadmap fee, and schedule the follow-up on the card for the date the client committed to (`schedule_followup`), not a drip interval.
4. Append the registry (`00_Deal_Registry.md`): rung, fee, invoice number, delivery date, the recommendation in one line, the proposal that follows, and the credit if any.
5. Hand to `proposal-scoping` (Mode 1) for the Step 1b or Steps 2 and 3 proposal. The Roadmap's "what could change it" section is that proposal's scope.

## What this skill measures (Goal 3)

Roadmaps sold per month, the 90-day conversion of Roadmaps to a Step 1 or 2 engagement (target 40%), and delivery time from request to send. The nightly export counts the Automator stages and the Productive Roadmap budgets; the Monday brief reports them. Every Roadmap that does not become an engagement within 90 days gets a lost reason on the opportunity.

## Guardrails

- **The Roadmap is a desktop planning product, not a scope of work or a fee proposal.** The memo says so in its first paragraph and its Terms.
- **No Productive writes before the request is paid or Chris approves the exception.** The screen and draft can proceed; the project setup waits.
- **Owner identity and site information stay inside the engagement.** Marketing reuse of a Roadmap (newsletter, case study) is anonymized and only with the client's consent (the Hillcrest rules from Sept 29, 2026).
- **Anything touching the Clean Water Act 404(f) agricultural exemption, or language that could read as concealing a resource from an agency, stops and goes to Chris.**
- **No fabricated citations.** If the code section or fee schedule is not in hand, mark `[FACT NEEDED]` and say so in the memo's "what would firm this up."
- **Send is explicit.** Draft, review, confirm the address, then send.
