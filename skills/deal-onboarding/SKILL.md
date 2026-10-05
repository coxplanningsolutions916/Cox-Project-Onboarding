---
name: deal-onboarding
description: "Onboard an approved/won Cox Planning Solutions deal — take a deal card with an approved proposal all the way to a live billable project in Productive plus a drafted down-payment invoice ready to send. Use when a proposal has been approved/signed (or the Automator card is Won) and the task is to stand up the engagement: create the Productive project + budget, copy the scoped services onto it, put the project on the dashboard, and draft the first (down-payment) invoice per the proposal's payment terms. Triggers: 'onboard [client/deal],' 'onboard this deal,' 'the proposal is approved/signed,' 'they signed,' 'kick off the project,' 'set up the project and down payment invoice,' 'take this deal to a project,' 'won deal,' or a deal card plus 'onboard.' Do NOT use for the pre-proposal paid deposit (use paid-findings-report), for building/pricing the proposal (use proposal-scoping), for a change order to an existing Task Order (use change-order-scoping), or for day-to-day ops."
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
2. **Project — from a template.** Run `list_templates` and pick the one that fits the scope:
   - **⛭ Biological Resource Clearance Letter (Reconnaissance-Level)** for a standalone recon-level bio letter. Keep everything.
   - **⛭ Cox Permitting Project (Master)** for everything else. It's the full 00–05 catalog, so **trim it to the deal**: read its lists with `list_task_lists(<template project_id>)`, map each service on the won sales deal to its task list (one list per billable service), and pass those names as `keep_task_lists`. Every other list is archived, which is reversible.

   Then call `create_project_from_template(template_project_id, name="<Client short> – <Project> (<address>)", company_id, keep_task_lists=[...])`. **Show Chris the keep list before running it.** Check `trim.not_found` in the result: a service with no matching list gets a list made with `create_task_list`.

   Due dates aren't copied, so schedule the tasks afterwards. If a service maps to no template at all (Roadmap, paid findings, one-off work), fall back to `create_project(name, company_id)`, which makes a blank project. Either tool applies the client type, PM Chris `1218809` and the client-project workflow. If an old MCP build 422s, use the API fallback in `references/down-payment-invoice.md`.
3. **Budget (Task Order)** — `create_budget(project_id, company_id, name="T.O. 1 — <Project>")`.
4. **Copy the estimate services onto the budget** — read the won **sales deal's** services (`list_services`), and for each, `add_service(deal_id=<new budget id>, name, price, phase, estimated_hours)`. (Services can't be re-parented off a Won sales deal, so they're re-created on the budget — the estimate is NOT re-keyed by hand; it's copied 1:1.)
5. **Registry** — set the registry links / file the onboarding entry so re-runs self-exclude.

Report the project id, the template used plus the task lists kept/archived, the budget id, and the services total ($) copied.

## Stage 1.5 — Put the project on the dashboard (not optional)

Every project gets a dashboard entry, whatever its size. The dashboard is the firm's
financial record — portfolio totals, the monthly billing cycle, AR and earned value all read
from it — so a project that is missing from it is missing from the firm's numbers entirely,
and its client statement is also the customer-facing PM view Cox sends as a link.

This used to be a step someone had to remember, and on 2026-09-11 five live engagements
worth **$92,992** were found running unregistered — Napa 55's $86,492 Cox job among them,
signed and logging time while invisible to every report. It is automatic now:

```bash
python ~/code/cox-productive-tools/discover_projects.py --pid=<project_id> --write
```

`onboard_from_deal.py --live` already calls this at the end of Stage 1, so the script path
needs nothing extra — but if you built the project through the MCP tools instead, run it
yourself before moving on. It registers the project in **both** registries (`project_ids.json`
for the dashboard and `qbo_crosswalk.json`, which every billing engine resolves a pid
through — writing only the first is what silently hid three projects until that same
morning) and scaffolds the budget file from the budget's services.

Then confirm it landed:

```bash
cd ~/code/cox-productive-tools && .venv/bin/python discover_projects.py   # must report nothing outstanding
```

The nightly refresh fills in schedule, earned value and billing figures; the statement builds
itself from there. Hand the client its link once the first invoice goes out.

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
- **No project without a dashboard.** Do not report onboarding complete until `discover_projects.py` reports nothing outstanding. A project that is not on the dashboard is not in the firm's financial numbers, however small the fee.
