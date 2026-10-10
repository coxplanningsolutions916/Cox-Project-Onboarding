# Proposal Generation — services in Productive → branded proposal

The estimate lives as **services on the Productive sales deal**, and the client proposal is
**generated from those services**. No workbook, no Automator proposal re-keying.

## 1. Find the sales deal

The deal already exists in the Productive CRM (pipeline `91540`) — the daily sync created it from the
Automator opportunity and tagged its note `[ghl:<oppid>]`. **Get the Productive deal id from the deal's
`00_Deal_Registry.md`** (it is recorded there — see `deal-registry.md`); that is the id you pass to
`add_service` / `list_services`. Note the Cox Productive MCP has **no deal-search tool** — it can add
and list services on a known deal id and read companies/people, but it cannot look a deal up by name,
set `deal_value`, or advance the stage. Those card fields are driven on the **Automator** side
(`update_opportunity`) and mirrored to Productive by the sync; if the registry has no deal id yet, the
sync has not created the deal — run/await it, then record the id.

## 2. Write each deliverable as a service

For every scoped deliverable, one `add_service` call:

```
add_service(deal_id=<deal>, name="1.1 Regulatory Confirmation & Density Analysis",
            price=2300, phase="01 Assessment", estimated_hours=14)
```

- **name** starts with the `Phase.Deliverable` code (`1.1`, `2.1`, `3.2`) so billing/onboarding can
  match it later. Keep the client-facing deliverable name.
- **price** is the proposed fee in **dollars** (not cents).
- **estimated_hours** is the hours basis behind the fee (from `estimating-basis.md` + judgment). It
  lands in the service's estimated-hours field — the correct home for the estimate. **Never type
  hours into the description.** To set hours on a line that already exists, use
  `update_service_price(service_id, estimated_hours=<h>)` (price and hours are independent — pass
  only what you're changing). Rates come from each person's rate card; never add a person as a
  service line.
- **phase** is one of the phase names below (maps to its service type):

  | phase name | service_type_id | | phase name | service_type_id |
  |---|---|---|---|---|
  | `00 Project Management` | 440153 | | `03 Planning` | 440156 |
  | `01 Assessment` | 440154 | | `04 Permits` | 440157 |
  | `02 Design` | 440155 | | `05 Compliance` | 440158 |

- **Pass-through / agency-fee lines** (County filing fees, etc.): add as a service too, named plainly
  with `pass-through` in it, e.g. `"R Pre-Application Filing Fee — LA County (pass-through)"`. Put it
  under `00 Project Management`. It counts toward the deal total but is flagged as non-labor downstream.
- **Phase 0 / PM** is usually "included in the deliverable fees" — don't add a priced PM line unless PM
  is separately billed.

## 3. Set the deal value — on the Automator side

Set the value **once, on the Automator opportunity**: `update_opportunity(monetary_value=<total $>)`
(services total incl. the pass-through). The daily sync mirrors that onto the Productive deal, converting
to cents automatically — so do **not** hand-set Productive `deal_value` (there is no MCP tool for it, and
the sync would overwrite it anyway). Productive stores `deal_value` in **cents** (a $10,000 deal =
`1000000`); this is why the value is driven from Automator dollars through the sync, not typed into
Productive. Automator and Productive must agree, and setting it in the one place keeps them that way.

## 4. Build the proposal content from the services

The proposal is a **task order on the Cox dashboard**, built from a content dict (the same shape as the
entitlement model's `projects/<key>/task_order_N.py`). The dashboard turns it into the Cox Word layout
(Ubuntu, navy/orange Cox Design System) and the signing page, so you write content, not layout.

| Content key | From |
|---|---|
| `number`, `subtitle` | the task-order number for this client ("1" for a new client) and a one-line title with the site |
| `meta` | `[["Client", ...], ["Attention", ...], ["Project", "<site, city>"], ["Services agreement", "Signed with Task Order <N>, <date>"]` (legacy client only; for MSA clients the form writes the `Master agreement` line, citing Cox MSA v2.0), `["Billing type", ...], ["Prepared by", "Chris Cox, CEO / Principal Planner, Cox Planning Solutions"], ["Date", "<Month D, YYYY>"]]`. Every task order also gets a `Form: Cox Task Order v2.0 (2026-10-09)` line |
| `intro` | two or three paragraphs: why this step, what it decides |
| `lines` | one per service: `{"code": "1.1", "name": <deliverable>, "scope": <1–3 sentences>, "fee": "$X,XXX"}`; pass-through lines plainly named |
| `total` | the services total, e.g. `"$16,950"` (add "(estimate)" for T&M) |
| `billing` (+ `milestones`) | the payment terms in words; milestones `[["Payment 1", "On signing", "$6,780"], ...]` (fixed fee defaults 40/40/20) |
| `schedule` | `[["<milestone>", "<target>"], ...]`, ranges; name what is outside Cox's control |
| `assumptions`, `exclusions` | one paragraph each; specific, never "assist with" |
| `agreement` | the governing agreement sentence. Required for a legacy client (the services agreement by date); optional with `msa_for` / `msa_date`, where the form writes one citing Cox MSA v2.0 |
| `msa_for` | new clients only: the client's legal name, which attaches the Cox MSA v2.0 as Part B (one signature signs both) |
| `msa_date` | a client who already signed the Cox MSA v2.0: the date they signed it |
| `legacy_amendment` | a client on the earlier combined agreement: the article to amend (`"4.6"`). Adds the v2.0 reimbursable amendment (subs cost +20%, other +15%, IRS mileage) going forward, and Section 3.2 cites it. Without it a legacy client stays on its old terms |
| `reimbursables` | optional override of the standard Section 3.2 Reimbursable expenses text |
| optional | `scope_note`, `optional_note` [paragraphs], `additional` `{text, rows [[service, when it applies, charge]]}` |

No internal hours, rates or cost floors (T&M quotes the client's hourly rate only). Worked examples:
`cox-entitlement-model/projects/tapscott-grand-view/task_order_2.py` (T&M with a retainer, existing client) and
`projects/7481-walnut/task_order_1.py` (fixed fee 40/40/20, new client + MSA).

## 5. Draft, release, sign — the pipeline moves itself

1. `draft_proposal(key, agreement_id, content, deal_id=<sales deal>, payment={amount, label, note})` — `key` is
   the project's dashboard key (the model profile key when one exists, otherwise a short slug of the site);
   `agreement_id` is `to<N>` (`co<N>` for a change order). It links the deal, its Automator card and the signer,
   creates the signature-record task, saves the draft and moves the Productive deal to **Proposal Prep**.
2. Chris reviews at `team_preview`; revise with the same call. `release_problems` lists what blocks release
   (placeholders, a missing signer email / Automator contact, the signature task, the deal or the card).
3. Chris releases (button on the preview, or `release_proposal` when he says) and **signs for Cox** — the
   client's link opens and the dashboard sets Automator **Proposal Sent** + Productive **Client Approval**.
4. Draft the client invitation (Gmail draft reply in the client's thread) with `get_proposal(...).client_link`.
5. The client signs — the dashboard sets Automator **Closed Won** + Productive **Won**, sends the confirmation
   with the payment options, and creates the invoice task. `deal-onboarding` takes it from there
   (`onboard_from_deal.py` copies these same services into the project budget).

**Expiry.** A proposal is open for 30 days from the day Cox signs it (`draft_proposal(..., valid_days=N)` to change
that). The client page shows the date; three days before, the morning routine drafts the last reminder; after it, the
client can't sign. The dashboard closes a quiet expired proposal by itself (deal Lost, card Closed Lost) and holds one the
client read in the last two weeks for Chris (EXPIRED on the deal watch). `extend_proposal` (reopens an expired one) and
`close_proposal` only when Chris says.

Do not move these stages by hand and do not send through Automator's proposal builder, Productive's proposal
template or SignNow.
