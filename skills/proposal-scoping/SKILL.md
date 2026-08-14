---
name: proposal-scoping
description: "Scope a new Cox Planning Solutions client engagement, write it into Productive as services on the sales deal, and generate the branded client proposal from those services (Mode 1). Use when a prospect has bitten and is ready to engage, and the task is to build the scope of work, budget and internal fee backup, timeline, and payment terms, then set the deliverables as Productive services and produce the client proposal. Triggers: 'scope a proposal,' 'new proposal,' 'build a proposal,' 'price this engagement,' 'scope of work,' 'fee backup,' 'budget for [client],' 'set up the services,' 'generate the proposal,' 'they want to move forward,' or a client plus a defined project ready to price. Do NOT use for the initial unpaid lead screen (use new-lead-intake-screen), for modifying an existing Task Order (use change-order-scoping), or for post-signature onboarding (use project-setup-workbook-asana-sync)."
---

# Proposal Scoping (Mode 1)

Build a new engagement's four scoping components in order, get Chris's approval on the margin, then **write the scope into Productive as services on the sales deal and generate the branded client proposal from those services.** This sits downstream of the new-lead intake screen (which produced a rough scope and budget range in the findings email) and upstream of onboarding.

**Productive is where the estimate lives; the proposal is generated from it.** The deliverables you scope become **services on the Productive sales deal** (that IS the estimate); the client proposal is **generated from those services**; on signature the same services become the **project budget** (via `onboard_from_deal.py`). Automator tracks the pipeline **stage + value**.

**Interim delivery (until SignNow Site License is active):** the generated proposal is a **handoff Chris populates manually into the Automator proposal builder** — you produce the proposal content once (from the Productive services), Chris does the one paste into Automator, and Automator sends it. You still never *re-key the estimate* (the numbers come straight from the services, not re-derived), and there is no double estimate. Once the SignNow connector is live, the generated proposal goes out for signature directly and the Automator paste drops away. The internal fee backup stays internal, as always.

## Before doing anything

| Step | Read first |
|------|-----------|
| **Any work on a deal — before anything else** | `references/deal-registry.md` (read the deal's `00_Deal_Registry.md` to restore state) |
| Building the budget, fee backup, or choosing a billing type | `references/billing-and-fees.md` |
| Estimating hours per deliverable | `references/estimating-basis.md` (empirical per-deliverable hour ranges from real Cox budgets) |
| Writing services to Productive + generating the proposal | `references/proposal-generation.md` |
| The internal record of the scoping decision (kept, not client-facing) | `references/handoff-memo-format.md` |
| Pricing a productized tool or non-standard engagement | `references/worked-example-aplus.md` |

**The deal is a live registry.** This project keeps each deal's state the way the client
project registry works post-onboarding: **read the deal's `00_Deal_Registry.md` first**, do the
step, **drive the card** (update the Automator opportunity — value, stage, contact — via the
Automator MCP; the daily sync mirrors value + stage onto the Productive deal, and services carry
the estimate), then **append a dated one-line entry** to the registry note. Nothing is done until
the log is written. Full convention + note format: `references/deal-registry.md`.

## Sequence — do not skip ahead

**A. Identify the engagement type.** Confirm client and legal entity, property/APN if applicable, what the client is trying to accomplish, and the DDL Step. Step 1 has two go/no-go tiers — 1a Screening (the subscription tool, instant first look) and 1b Initial Screening Report (the paid desktop screen, $2,500 standard; $1,000 only for small parcels); deeper paid commitment begins at Step 2. For engagements that do not map to the DDL (productized tools, public-agency contracts, SWPPP bids), use the DDL phases for internal organization but label the engagement by its actual scope type. If Chris has already set the step, confirm and proceed — do not re-debate it.

**B. Scope of work.** Build the deliverable list phase by phase using `Phase.Deliverable` codes (e.g., 1.1, 1.2). For each: a one-to-three-sentence description suitable for the proposal body; flag sequencing dependencies, subconsultant needs, and any contingent or optional items. Be specific about what is and is not included. Reference the Component Library for approved deliverable language where available.

**C. Budget and fee backup.** Estimate hours by role per deliverable using `references/estimating-basis.md` — start at the empirical median for each deliverable and place the project inside its range for the parcel, species count, agency, and permit pathway (do not estimate from the Asana template task chains; they under-state the analytical deliverables). Apply the rate table, add subconsultant costs at +15%, add 8% PM (go-forward), and round up to the proposed fee. For a **new** engagement there are no actuals to ground against (and Napa 55's Harvest history is Barnett legacy — never pull it); the basis medians are the anchor. Build the internal fee backup against the **real cost floor**, not MSRP — Claude-assisted work compresses actual labor, and pricing should reflect real margin (see `references/billing-and-fees.md`). Show Chris the fee backup table (hours × cost vs. proposed fee, with margin) before finalizing. He must approve the margin. For fixed fee, verify proposed fee ≥ cost. For T&M, present NTE = cost + contingency (~10%). For subscription/productized, present cost-to-produce, the recommended price, and the funnel logic. Hold ambiguous fee decisions for Chris's explicit confirmation rather than deciding them.

**D. Timeline.** Phase-level: start, duration, key constraints (agency windows, seasonal survey requirements, client decision points), and what is outside Cox's control (agency response times, permit processing).

**E. Payment terms.** Fixed fee defaults to 40/40/20 (signature / midpoint / final); adjust to the scope and amount (a small build may be 50/50 or paid up front). T&M is monthly, 30-day terms, NTE, contingency held in reserve. Subscription is annual or monthly per `references/billing-and-fees.md`. VIP and public-agency clients (e.g., Panattoni) typically take monthly invoicing with no down payment — confirm.

**F. Write the scope into Productive as services.** Only after Chris approves scope, budget, timeline, and payment. Find the client's **sales deal** in the Productive CRM pipeline (`91540`) — it is the one already synced from the Automator opportunity (matched by client name / the `[ghl:…]` note tag). For each scoped deliverable, add a **service** to that deal: `add_service(deal_id, name="1.1 <deliverable name>", price=<proposed fee $>, phase="01 Assessment")` — the name carries the `Phase.Deliverable` code, the phase maps to its service type. Add pass-through / agency-fee lines the same way (name them plainly, e.g. `"R Pre-Application Filing Fee — <agency> (pass-through)"`). The services now ARE the estimate; do not also build it in a workbook. See `references/proposal-generation.md` for the exact call shapes, phase→service-type map, and the pass-through convention.

**Then drive the card + log it.** Set the **Automator** opportunity's `monetary_value` to the services total (`update_opportunity(monetary_value=<total $>)`) — the daily sync mirrors that value (and stage) onto the Productive deal in cents, so Automator and Productive agree without hand-editing Productive's `deal_value`. Advance the Automator stage to Proposal Prep. Append a dated line to `00_Deal_Registry.md`: `scope drafted — <n> deliverables` and `budget set — fee $<total>`. See `references/deal-registry.md`.

**G. Generate the branded client proposal from those services.** Read the deal's services (`list_services`) and render the Cox proposal — four sections (Introduction, Scope of Work, Timeline, Investment) built from the scoped intro, the services + fees, the timeline, and the payment schedule, with the signature block. Follow `references/proposal-generation.md` (which carries the Cox-branded HTML template) and the `cox-document-formatting` brand rules. Present it to Chris.

**Delivery — interim (Automator) vs target (SignNow).** Until the SignNow Site License is active, the generated proposal is the **handoff Chris uses to manually populate the proposal in Automator** — the four-section content (deliverables, descriptions, fees, timeline, payment schedule) is paste-ready for the Automator proposal builder. Do not attempt to send it through Productive (the Productive proposal template is invoice-like and was set aside) or through SignNow (not yet wired). On approval, Chris sends it from Automator and advances the opportunity to **Proposal Sent** (`update_opportunity(stage="Proposal Sent")`). Append to `00_Deal_Registry.md`: `proposal generated — <file>` and, on send, `proposal sent (Automator) — stage → Proposal Sent`. Once the SignNow MCP connector is live, this same generated proposal goes out for signature directly (see the SignNow note in `references/proposal-generation.md`) and the manual Automator step drops away.

**H. Internal record + fee backup.** Save the internal fee backup separately as `[Client]_FeeBackup_[ProjectShortName]_INTERNAL.xlsx`; it never goes to the client. Keep the scoping record (the handoff-memo format in `references/handoff-memo-format.md`) as the internal narrative of the decision — it is no longer a hand-off for someone to build the proposal (the proposal is generated in G), but it remains the readable record of scope, assumptions, and open items.

## Guardrails

- The firm is **Cox**, never "CPS" — including internal project-number prefixes.
- Brand: Arial, Cox Navy and Cox Gold, no exclamation marks, no buzzwords, specific regulatory citations, range-based estimates with confidence tags.
- No guaranteed approvals or valuation outcomes. Include the standard non-guarantee in the terms.
- Vague scope language ("assist with," "support," "help coordinate") is not acceptable — be specific.
- Fee backup, hours, and cost floors are internal and never appear in client-facing material.
