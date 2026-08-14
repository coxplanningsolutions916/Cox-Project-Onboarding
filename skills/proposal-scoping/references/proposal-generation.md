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

## 4. Generate the branded proposal

Read the services (`list_services`) and render the four-section Cox proposal. **Follow the
`cox-document-formatting` brand rules** (Navy `#1B2A6B`, Gold `#F5A623`, Arial; ranges not points; no
guaranteed outcomes; no internal hours/rates; no exclamation marks; specific regulatory citations).

**Structure (the only four sections):**
1. **Introduction** — the scoped intro in Chris's voice (the "why this step, what it decides" framing).
2. **Scope of Work** — the services grouped by phase; each shows the deliverable name, a 1–3 sentence
   description, and its fee. Include any title-report / client-provided-item notes.
3. **Timeline** — phase-level, range-based; name what is outside Cox's control (agency response times).
4. **Investment** — the fee table (each deliverable + fee → Cox Professional subtotal; pass-through line
   shown separately; Total Fixed Fee), the pass-through credit/excess note (Article 4.6), and the
   **payment schedule** (fixed fee defaults 40/40/20). End with the standard non-guarantee sentence and
   the **Authorization / signature block** (Consultant: Chris Cox / Cox Planning Solutions; Client: the
   legal entity + authorized representative).

**Template.** A working Cox-branded HTML proposal (masthead, meta strip, the four sections, deliverable
cards, investment table, payment cards, signature block) is the canonical layout — navy masthead with a
gold accent rule, uppercase navy section labels, tabular-aligned fees, committed to the light "paper"
look. Reuse that HTML skeleton and swap the content from the services. Save the output for Chris's review
before it goes to the client.

## 5. Deliver the proposal + advance the pipeline

**Interim (current, until SignNow Site License is active).** The generated proposal is the **handoff
Chris uses to populate the Automator proposal manually**. Give him the four-section content paste-ready
(deliverables + descriptions + fees, timeline, payment schedule) plus the branded HTML for reference.
Do NOT send via Productive — its proposal template is invoice-like and was set aside. On Chris's approval
he sends from Automator and advances the opportunity to **Proposal Sent**
(`update_opportunity(stage="Proposal Sent")`).

**Target (once the SignNow MCP connector is live).** The same generated proposal (rendered to branded
PDF from the Productive services) goes out for signature **directly through SignNow** — no manual
Automator paste. The signed-webhook flips the opportunity to Won, drafts the down-payment invoice, and
triggers onboarding. Until then, Automator remains the send + e-sign channel.

Either way, on signature → the opportunity is marked **Won**, which flows into onboarding
(`onboard_from_deal.py` copies these same services into the project budget).
