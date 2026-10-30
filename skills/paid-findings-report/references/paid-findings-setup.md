# Paid findings — QBO invoice (and the Productive exception)

The exact tool sequence to invoice a Step 1 planner level sold off the portal (a Planner Review, a Strategy Session once Chris has confirmed the tier, or a Site Walk) with a pay link. Prices come from `canon/pilot.yaml` on the day. Uses the **QBO** MCP for the invoice and, only in the exception below, the **cox-productive** MCP.

## A. Productive (only when the lead is already a client)

A planner level does not get its own Productive project: the project starts at a signed task order (`proposal-scoping` → `deal-onboarding`). The retired findings-deposit setup (company → project → "T.O. 1 — Paid Initial Findings" + "T.O. 2 … credit applies" budgets) is no longer used for new sales.

If the lead is already a Productive client with a live project, the level can sit there as a service: `add_service(deal_id=<the open budget>, name="<Level> — <address>", price=<fee>, phase="Q1 Screening")`, any hours basis in `estimated_hours`, never the name. Otherwise ask Chris before creating anything in Productive. Review work is tracked on the **ZZ Roadmap reviews** list (`2941807`, project `983286`).

## B. QBO: customer → invoice → send

Payment rail is **QBO pay links** (card/ACH) — one source of truth; paid status syncs back to Productive via the standard QBO→Productive payment sync. Do not use a separate PayPal/Automator rail for these.

1. **Customer.** `qbo_contact_search_customer(customer_name=<entity>)`; if `found=false`, `qbo_contact_create_customer(display_name=<entity>, first_name/last_name, additional_params={billing_address, email})`.
2. **Line item.** The invoice line uses the **phase service item** so it maps to the phase in QBO — `qbo_catalog_search_products(["01 Assessment"])` to get its `product_id` (Cox QBO carries `00 Project Management`…`05 Compliance` as service items). `taxable=false`.
3. **Create the invoice (does NOT email).** `qbo_sales_create_invoice` with:
   - `customer_data.customer_id` = the QBO customer local id
   - one line item: the `01 Assessment` product_id, `amount=<the level's fee from the feed>`, `taxable=false`, a description naming the level, tier and address, e.g. "Strategy Session (standard) — 1200 Elm St" (with a ≤100-char `summarized_description`)
   - a negative line for any credit the feed gives (a paid Planner Review against a Strategy Session on the same site, up to $500), described "Credit — Planner Review <invoice #>"
   - for a Site Walk, expenses on their own line at cost (mileage, travel), described plainly
   - `customer_address_info.note_to_customer` = the payment-options memo + credit line: *"Payment options: (1) Pay online by bank transfer — a $25 convenience fee applies. (2) Mail a check payable to Cox Planning Solutions, 1510 J Street, Suite 100, Sacramento, CA 95814 — no fee. (3) Prefer credit card? Reply and we'll send a link — 3% fee."* For a Site Walk add: *"This fee is credited in full toward Field Verification if you sign within 90 days."* For a Planner Review add: *"This fee comes off a Strategy Session on this site, up to $500."*
   - `invoice_dates` (transaction date today; due date ~net 7)
   - **Do NOT enable business payment methods** (leave `enable_cc_payment`/`enable_bank_payment`/`enable_paypal_payment` off). The account-level **"Your client pays the fee"** is ON, so every invoice automatically offers **check (free) + bank transfer online ($25 flat, client pays)** and Cox never eats a fee. For a card-on-request client: enable Cards on that one invoice + add a **3% line item**.
   - **Do NOT set a custom `invoice_reference_number`** — leave it blank so QBO assigns the next chronological number (custom numbering is ON; overriding it breaks the sequence — the Ochi pilot was wrongly created as `OCHI-ASSESS-01` and had to be renumbered to `3414`).
4. **Confirm the email, then send.** `qbo_sales_send_invoice(invoice_id, customer, reference_number, delivery_info={delivery_address:<client email>, subject, message})`. Pass `invoice_id`/`reference_number` exactly as returned by create. Sending is the one explicit, client-facing action — never auto-send before the address is confirmed.

## Cox defaults (as of 2026-08)

- Client-project workflow `60022` · PM (Chris) `1218809` · Subsidiary `59749` · project_type_id `2` (client)
- QBO `01 Assessment` service item = product_id `85`
