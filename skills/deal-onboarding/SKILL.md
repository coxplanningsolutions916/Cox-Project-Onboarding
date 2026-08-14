---
name: deal-onboarding
description: "Onboard an approved/won Cox Planning Solutions deal — take a deal card with an approved proposal all the way to a live billable project in Productive plus a drafted down-payment invoice ready to send. Use when a proposal has been approved/signed (or the Automator card is Won) and the task is to stand up the engagement: create the Productive project + budget, copy the scoped services onto it, and draft the first (down-payment) invoice per the proposal's payment terms. Triggers: 'onboard [client/deal],' 'onboard this deal,' 'the proposal is approved/signed,' 'they signed,' 'kick off the project,' 'set up the project and down payment invoice,' 'take this deal to a project,' 'won deal,' or a deal card plus 'onboard.' Do NOT use for the pre-proposal paid deposit (use paid-findings-report), for building/pricing the proposal (use proposal-scoping), for a change order to an existing Task Order (use change-order-scoping), or for day-to-day ops."
---

# Deal Onboarding (approved proposal → project + down-payment invoice)

The sales→delivery bridge. Downstream of `proposal-scoping` (which built the estimate as **services on the Productive sales deal** and produced the proposal) and, when relevant, `paid-findings-report` (which may have collected a credited deposit). This skill converts an **approved/won** deal into a **live billable project** and drafts the **down-payment invoice** — then you approve and it sends.

**Non-negotiables:**
- **The approved proposal is the contract — its payment terms are the source of truth.** Read them; don't assume. 40% down is only the *default for fixed-fee* when the proposal doesn't say otherwise.
- **Draft → you approve → send.** Never auto-send a client invoice.
- **Creating a project is a client commitment** — one deal at a time, explicit, never bulk-auto.

## Stage 0 — Restore state & confirm the deal is real

1. Read the deal's **`00_Deal_Registry.md`** at the top of `Sales/Proposals/[Lead]/` (Google Drive MCP) and the **approved proposal** in that folder. Pull: client legal entity + contact/email, project name, property/APN, the scoped **services** (the estimate), the **contract value**, and the **payment terms** (down-payment %, milestones, or T&M/monthly).
2. Confirm the deal is actually **approved/Won** (signed proposal, or Automator card in Closed Won). If it's not, stop and say so — do not onboard an unapproved deal.
3. If a **paid-findings deposit** was collected (see `paid-findings-report`), note the amount to **credit** against the down payment.

## Stage 1 — Create the Productive project + budget, copy the services

Use the **cox-productive** MCP. This mirrors `~/code/cox-productive-tools/onboard_from_deal.py` (the canonical logic; run it directly if you prefer the script path). Order:

1. **Company** — `list_companies(name_contains=)` to dedupe; `create_company` if new.
2. **Project** — `create_project(name="<Client short> – <Project> (<address>)", company_id)`. (Client type, workflow `60022`, PM Chris `1218809` are applied by the tool — the create_project/create_budget bugs were patched 2026-08-12 so these no longer 422; if an old build still errors, use the API fallback in `references/down-payment-invoice.md`.)
3. **Budget (Task Order)** — `create_budget(project_id, company_id, name="T.O. 1 — <Project>")`.
4. **Copy the estimate services onto the budget** — read the won **sales deal's** services (`list_services`), and for each, `add_service(deal_id=<new budget id>, name, price, phase, estimated_hours)`. (Services can't be re-parented off a Won sales deal, so they're re-created on the budget — the estimate is NOT re-keyed by hand; it's copied 1:1.)
5. **Registry** — set the registry links / file the onboarding entry so re-runs self-exclude.

Report the project id, budget id, and the services total ($) copied.

## Stage 2 — Draft the down-payment invoice (per the proposal's terms)

Read the payment terms from Stage 0 and follow them — they govern. Then create the invoice as a **draft** (see `references/down-payment-invoice.md` for the exact QBO tool sequence):

- **Fixed-fee, terms unspecified →** default **40% of contract** down at onboarding.
- **Fixed-fee with a stated schedule →** use the proposal's schedule (e.g., 50/50, milestone splits).
- **T&M NTE / public-agency / monthly (e.g., Panattoni, agencies) →** typically **no down payment** — first invoice is monthly-on-actuals per the terms. If so, create the project but draft **no** down-payment invoice; note that billing is monthly.
- **Credit any paid-findings deposit** (e.g., a $500 findings fee) as a negative line so it nets against the down payment.

Invoice construction (all in `references/down-payment-invoice.md`): QBO customer (search/create), **phase service line items** (map to `00…05` service items), **auto-numbered (do NOT set a custom reference)**, the standard **payment-options memo**, and **no business payment methods enabled** (the account-level "client pays the fee" gives check + client-pays ACH; Cox never eats a fee).

## Stage 3 — Review, then send on approval

Present a single summary for review:
- Project + budget created (ids), services copied ($ total)
- **Payment terms as read from the proposal** — call these out explicitly for confirmation (this is the "review closely" step)
- The drafted down-payment invoice: amount, % of contract, any deposit credit, due date — **unsent**

On your **explicit go-ahead**, send it: `qbo_sales_send_invoice(invoice_id, customer, reference_number, delivery_info={delivery_address:<client email>})`. Never before.

## Stage 4 — Advance the pipeline

- Update the **Automator** card to onboarded/Closed Won (`update_opportunity`), append the `00_Deal_Registry.md` log with the onboarding date, project/budget ids, and the down-payment invoice #.
- Payment, once received, auto-syncs QBO→Productive via the standard payment sync.

## Guardrails

- **Proposal terms win.** Never override the approved proposal's payment schedule with a default; 40% is only the fixed-fee fallback.
- **Draft → approve → send.** No auto-send. Confirm the client email before sending.
- **No fee to Cox.** Invoices go out check + client-pays ACH (never accept cards/ACH as business methods); card-on-request = enable cards on that one invoice + a 3% line.
- **Chronological invoice #s.** Let QBO auto-number; never set a custom reference.
- **One deal at a time.** Onboarding is a client commitment — explicit per deal, never a bulk sweep. (`onboard_from_deal.py --scan` is read-only and only *lists* deals awaiting onboarding for the brief.)
- **Credit deposits.** Any paid-findings deposit credits the down payment.
