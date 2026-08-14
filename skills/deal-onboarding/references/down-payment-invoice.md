# Down-payment invoice — QBO mechanics

Draft the onboarding invoice per the proposal's payment terms. Same rail as every Cox invoice: **check (free) + client-pays ACH ($25 flat), Cox never eats a fee.** Draft only — send is a separate, approved step.

## Amount

- **Fixed-fee, unspecified terms:** `round(contract_value * 0.40)` = 40% down.
- **Fixed-fee with a schedule in the proposal:** use it (50/50, milestone %, etc.).
- **T&M NTE / monthly / public-agency:** usually **no** down-payment invoice — first bill is monthly-on-actuals. Create the project; skip this invoice; note monthly billing.
- **Deposit credit:** if a paid-findings deposit was collected (e.g. $500), add it as a **negative line** so it nets out (e.g., 40% of $10,000 = $4,000, minus $500 credit = $3,500 due).

## Build (QBO MCP)

1. **Customer** — `qbo_contact_search_customer(customer_name=<legal entity>)`; if `found=false`, `qbo_contact_create_customer(display_name, first/last, additional_params={billing_address, email})`.
2. **Line items** — use the **phase service items** so it maps in QBO (`qbo_catalog_search_products(["00 Project Management","01 Assessment", …])`; Cox carries `00…05`). For a simple % down payment, one line on the dominant phase (or "00 Project Management") is fine — description e.g. *"40% down payment — <Project> (Task Order 1)."* `taxable=false`. Add the **deposit-credit** as a second, negative-amount line (`amount = -<deposit>`, description *"Credit — paid initial findings deposit"*).
3. **Create the invoice (does NOT email)** — `qbo_sales_create_invoice` with:
   - `customer_data.customer_id`
   - the line item(s) above (with ≤100-char `summarized_description` where needed)
   - `customer_address_info.note_to_customer` = the standard memo: *"Payment options: (1) Pay online by bank transfer — a $25 convenience fee applies. (2) Mail a check payable to Cox Planning Solutions, 1510 J Street, Suite 100, Sacramento, CA 95814 — no fee. (3) Prefer credit card? Reply and we'll send a link — 3% fee."*
   - `invoice_dates` (transaction date today; due date per the proposal terms, else net-15)
   - **Do NOT enable business payment methods** (leave `enable_cc_payment`/`enable_bank_payment`/`enable_paypal_payment` off). Account-level "Your client pays the fee" is ON → every invoice auto-offers check (free) + bank transfer ($25, client pays).
   - **Do NOT set a custom `invoice_reference_number`** — leave blank so QBO assigns the next chronological number (custom numbering is ON; overriding breaks the sequence).
4. **Send only after approval** — `qbo_sales_send_invoice(invoice_id, customer, reference_number, delivery_info={delivery_address:<client email>})`, passing `invoice_id`/`reference_number` exactly as returned by create.

## Productive project/budget (cox-productive MCP) — canonical values

- Client-project workflow `60022` · PM (Chris) `1218809` · Subsidiary `59749` · `project_type_id 2` (client)
- **API fallback** if an old MCP build 422s on `create_project`/`create_budget` (should be patched as of 2026-08-12): `~/code/cox-productive-tools` (`.venv/bin/python`, `import productive_client as pc`) — POST `/projects` with `attributes.project_type_id:2` + company/workflow/project_manager rels; POST `/deals` with `budget:true, deal_type_id:2, date:<today>, currency:"USD"` + company/project/responsible/subsidiary rels. `onboard_from_deal.py <sales_deal_id> --live` does the whole project+budget+copy-services+registry in one shot.

## QBO phase service item ids (as of 2026-08)

`00 Project Management`=71 · `01 Assessment`=85 · `02 Design`=81 · `03 Planning`=84 · `04 Permits`=83 · `05 Compliance`=82 · generic `Service`=54 · `Expenses`=78. (Confirm via `qbo_catalog_search_products` if unsure.)
