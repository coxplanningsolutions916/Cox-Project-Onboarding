# Paid Findings — Productive setup + QBO invoice

The exact tool sequence to stand a paying lead up as a client and get the deposit invoiced with a pay link. Uses the **cox-productive** MCP for Productive and the **QBO** MCP for the invoice.

## A. Productive: company → project → two budgets → service

1. **Dedupe, then create the company.** `list_companies(name_contains=...)` first. If absent, `create_company(name=<legal/vesting entity>)`. Keep the id.
2. **Create the project.** `create_project(name="<Client short> – <Project> (<address>)", company_id=<id>)`. It defaults to the client/billable project type (`project_type_id=2`), the client-project workflow, and Chris as PM.
3. **Create the two budgets** (both on the project):
   - `create_budget(project_id, company_id, name="T.O. 1 — Paid Initial Findings (<subject>)")`
   - `create_budget(project_id, company_id, name="T.O. 2 — <follow-on work> (SOW — TBD; $<amount> credit applies)")`
4. **Add the deposit deliverable** to T.O. 1: `add_service(deal_id=<T.O.1 id>, name="Initial Findings Report — <subject> (<address>)", price=<500|1000>, phase="01 Assessment")`. If you tracked an hours basis, pass `estimated_hours=` — never put hours in the name.

> **If `create_project` or `create_budget` returns HTTP 422** for a missing `project_type_id` / `date` (older MCP build not yet redeployed), fall back to the direct Productive API in `~/code/cox-productive-tools` (`.venv/bin/python`, `import productive_client as pc`): POST `/projects` with `attributes.project_type_id: 2` + relationships company / workflow `60022` / project_manager `1218809`; POST `/deals` with `budget:true, deal_type_id:2, date:<today>, currency:"USD"` + relationships company / project / responsible `1218809` / subsidiary `59749`. The patched MCP supplies these itself.

## B. QBO: customer → invoice → send

Payment rail is **QBO pay links** (card/ACH) — one source of truth; paid status syncs back to Productive via the standard QBO→Productive payment sync. Do not use a separate PayPal/Automator rail for these.

1. **Customer.** `qbo_contact_search_customer(customer_name=<entity>)`; if `found=false`, `qbo_contact_create_customer(display_name=<entity>, first_name/last_name, additional_params={billing_address, email})`.
2. **Line item.** The invoice line uses the **phase service item** so it maps to the phase in QBO — `qbo_catalog_search_products(["01 Assessment"])` to get its `product_id` (Cox QBO carries `00 Project Management`…`05 Compliance` as service items). `taxable=false`.
3. **Create the invoice (does NOT email).** `qbo_sales_create_invoice` with:
   - `customer_data.customer_id` = the QBO customer local id
   - one line item: the `01 Assessment` product_id, `amount=<500|1000>`, `taxable=false`, a description naming the report + that it credits the SOW (with a ≤100-char `summarized_description`)
   - `customer_address_info.note_to_customer` = the payment-options memo + credit line: *"Payment options: (1) Pay online by bank transfer — a $25 convenience fee applies. (2) Mail a check payable to Cox Planning Solutions, 1510 J Street, Suite 100, Sacramento, CA 95814 — no fee. (3) Prefer credit card? Reply and we'll send a link — 3% fee. This deposit is credited toward your scope of work if you proceed."*
   - `invoice_dates` (transaction date today; due date ~net 7)
   - **Do NOT enable business payment methods** (leave `enable_cc_payment`/`enable_bank_payment`/`enable_paypal_payment` off). The account-level **"Your client pays the fee"** is ON, so every invoice automatically offers **check (free) + bank transfer online ($25 flat, client pays)** and Cox never eats a fee. For a card-on-request client: enable Cards on that one invoice + add a **3% line item**.
   - **Do NOT set a custom `invoice_reference_number`** — leave it blank so QBO assigns the next chronological number (custom numbering is ON; overriding it breaks the sequence — the Ochi pilot was wrongly created as `OCHI-ASSESS-01` and had to be renumbered to `3414`).
4. **Confirm the email, then send.** `qbo_sales_send_invoice(invoice_id, customer, reference_number, delivery_info={delivery_address:<client email>, subject, message})`. Pass `invoice_id`/`reference_number` exactly as returned by create. Sending is the one explicit, client-facing action — never auto-send before the address is confirmed.

## Cox defaults (as of 2026-08)

- Client-project workflow `60022` · PM (Chris) `1218809` · Subsidiary `59749` · project_type_id `2` (client)
- QBO `01 Assessment` service item = product_id `85`
