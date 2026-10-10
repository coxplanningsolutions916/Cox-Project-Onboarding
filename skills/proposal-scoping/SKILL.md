---
name: proposal-scoping
description: "Scope a new Cox Planning Solutions client engagement, write it into Productive as services on the sales deal, and generate the branded client proposal from those services (Mode 1). Use when a prospect has bitten and is ready to engage, and the task is to build the scope of work, budget and internal fee backup, timeline, and payment terms, then set the deliverables as Productive services and produce the client proposal. Triggers: 'scope a proposal,' 'new proposal,' 'build a proposal,' 'price this engagement,' 'scope of work,' 'fee backup,' 'budget for [client],' 'set up the services,' 'generate the proposal,' 'they want to move forward,' or a client plus a defined project ready to price. Do NOT use for the initial unpaid lead screen (use new-lead-intake-screen), for modifying an existing Task Order (use change-order-scoping), or for post-signature onboarding (use deal-onboarding)."
---

# Proposal Scoping (Mode 1)

Build a new engagement's four scoping components in order, get Chris's approval on the margin, then **write the scope into Productive as services on the sales deal and generate the branded client proposal from those services.** This sits downstream of the new-lead intake screen (which produced a rough scope and budget range in the findings email) and upstream of onboarding.

**Productive is where the estimate lives; the proposal is generated from it.** The deliverables you scope become **services on the Productive sales deal** (that IS the estimate); the client proposal is **generated from those services**; on signature the same services become the **project budget** (via `onboard_from_deal.py`). Automator tracks the pipeline **stage + value**.

**Delivery is the Cox dashboard (since 2026-10-07).** The proposal is drafted on the dashboard with `draft_proposal` (cox-productive MCP), Chris reviews it at the team preview link, releases it, and signs for Cox; the client signs on their project link. **The dashboard moves the pipeline itself** — Cox's signature (the client's link opens) sets Automator *Proposal Sent* + Productive *Client Approval*; the client's signature sets Automator *Closed Won* + Productive *Won* (which `deal-onboarding` picks up). Never move those stages by hand. Automator's proposal builder and SignNow are no longer the send path. The internal fee backup stays internal, as always.

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

**C. Budget and fee backup.** Estimate hours by role per deliverable using `references/estimating-basis.md` — start at the empirical median for each deliverable and place the project inside its range for the parcel, species count, agency, and permit pathway (do not estimate from the Asana template task chains; they under-state the analytical deliverables). Apply the rate table, add subconsultant costs at +20% (other reimbursables +15%; MSA v2.0), add 8% PM (go-forward), and round up to the proposed fee. For a **new** engagement there are no actuals to ground against (and Napa 55's Harvest history is Barnett legacy — never pull it); the basis medians are the anchor. Build the internal fee backup against the **real cost floor**, not MSRP — Claude-assisted work compresses actual labor, and pricing should reflect real margin (see `references/billing-and-fees.md`). Show Chris the fee backup table (hours × cost vs. proposed fee, with margin) before finalizing. He must approve the margin. For fixed fee, verify proposed fee ≥ cost. For T&M, present NTE = cost + contingency (~10%). For subscription/productized, present cost-to-produce, the recommended price, and the funnel logic. Hold ambiguous fee decisions for Chris's explicit confirmation rather than deciding them.

**D. Timeline.** Phase-level: start, duration, key constraints (agency windows, seasonal survey requirements, client decision points), and what is outside Cox's control (agency response times, permit processing).

**E. Payment terms.** Fixed fee defaults to 40/40/20 (signature / midpoint / final); adjust to the scope and amount (a small build may be 50/50 or paid up front). T&M is monthly, 30-day terms, NTE, contingency held in reserve. Subscription is annual or monthly per `references/billing-and-fees.md`. VIP and public-agency clients (e.g., Panattoni) typically take monthly invoicing with no down payment — confirm.

**F. Write the scope into Productive as services.** Only after Chris approves scope, budget, timeline, and payment. **First, get the deal id.** Use the id recorded in `00_Deal_Registry.md` if it is there; otherwise call `find_deals(query="<client name>", company_id="<id if known>", ghl_opp_id="<opp id if known>")` to locate the client's **sales deal** in the Productive CRM pipeline (`91540`) — it is the one synced from the Automator opportunity (matched by client name / the `[ghl:…]` note tag). **If no deal is found, stop and ask Chris for the deal URL — do not record "pending sync" and move on. That is how the estimate ends up on the wrong object** (services written to a fresh project budget instead of the real sales deal, then backfilled by hand). Record the deal id in the registry once you have it. With the deal id in hand, for each scoped deliverable add a **service** to that deal: `add_service(deal_id, name="1.1 <deliverable name>", price=<proposed fee $>, phase="01 Assessment")` — the name carries the `Phase.Deliverable` code, the phase maps to its service type. Add pass-through / agency-fee lines the same way (name them plainly, e.g. `"R Pre-Application Filing Fee — <agency> (pass-through)"`). The services now ARE the estimate; do not also build it in a workbook. See `references/proposal-generation.md` for the exact call shapes, phase→service-type map, and the pass-through convention.

**Then drive the card + log it.** Set the **Automator** opportunity's `monetary_value` to the services total (`update_opportunity(monetary_value=<total $>)`) — the daily sync mirrors that value (and stage) onto the Productive deal in cents, so Automator and Productive agree without hand-editing Productive's `deal_value`. Leave the Automator card at Prepare Proposal (Automator has no Proposal Prep stage; `draft_proposal` moves the Productive deal to Proposal Prep in G). Append a dated line to `00_Deal_Registry.md`: `scope drafted — <n> deliverables` and `budget set — fee $<total>`. See `references/deal-registry.md`.

**G. Draft the proposal on the dashboard from those services.** Read the deal's services (`list_services`) and build the task-order **content** from them — intro, one `lines` row per service (code, deliverable, scope, fee), total, billing and payment milestones, schedule, assumptions, exclusions, agreement — in the shape `draft_proposal` documents (`references/proposal-generation.md` §4 has the mapping and a worked example). Then call `draft_proposal(key, agreement_id, content, deal_id=<sales deal>, payment=<due on signing, if any>)`: it fills the deal, the Automator card and the client signer from the deal's contact, creates the Productive signature-record task, saves the draft, and moves the Productive deal to Proposal Prep. Anything still unknown goes in `[square brackets]` — a draft with placeholders can't be released. Give Chris the returned `team_preview` link and any `release_problems`; revise with another `draft_proposal` call (same key and id) until he is satisfied. `list_proposals` shows the ids already taken; a new client's first task order sets `content.msa_for` to attach the Master Services Agreement. Follow the `cox-document-formatting` voice rules for the text.

**Delivery — the dashboard.** Chris releases the draft (Release for signature on the team preview, or `release_proposal` **only when he says so**) and signs for Cox there. That opens the client's link and moves the cards (Automator Proposal Sent, Productive Client Approval). Get the client link from `get_proposal` (`client_link`) and draft the invitation email to the signer as a **Gmail draft reply in the client's thread** — Chris sends it. When the client signs, the dashboard sets Closed Won / Won, emails the confirmation and creates the invoice task; hand off to `deal-onboarding`. Append to `00_Deal_Registry.md`: `proposal drafted (dashboard) — <key>/<id> v<n>`, then `proposal sent (dashboard) — Cox signed, client link sent`, then `signed — Won`. Do not send proposals through Automator's proposal builder or Productive's proposal template.

**H. Internal record + fee backup.** Save the internal fee backup separately as `[Client]_FeeBackup_[ProjectShortName]_INTERNAL.xlsx`; it never goes to the client. Keep the scoping record (the handoff-memo format in `references/handoff-memo-format.md`) as the internal narrative of the decision — it is no longer a hand-off for someone to build the proposal (the proposal is generated in G), but it remains the readable record of scope, assumptions, and open items.

## Guardrails

- The firm is **Cox**, never "CPS" — including internal project-number prefixes.
- Brand: Arial, Cox Navy and Cox Gold, no exclamation marks, no buzzwords, specific regulatory citations, range-based estimates with confidence tags.
- No guaranteed approvals or valuation outcomes. Include the standard non-guarantee in the terms.
- Vague scope language ("assist with," "support," "help coordinate") is not acceptable — be specific.
- Fee backup, hours, and cost floors are internal and never appear in client-facing material.
