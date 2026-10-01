# Roadmap — systems sequence (Automator, Productive, QuickBooks, Drive)

## A. Automator (the live card)
1. Find or create the opportunity on the Sales Pipeline; confirm the contact is the decision maker (`create_contact` + `update_opportunity(contact_id=...)` if not).
2. Set `source` to one canonical value: Google Ads, LinkedIn, Direct Mail, Referral, Existing Client, Website, BIA Workshop, Podcast, ABM, Meta Ads, Public Agency. Web submissions carry it from the form and UTM capture; phone leads get it from the "how did you hear about us" question.
3. Stage **Roadmap Requested** on request; **Roadmap Delivered** on send. (Stages added Sept 30, 2026; they sit after Incoming Lead on the board.) Value = the rung fee on request; the Step 1 or 2 proposal value replaces it when that proposal goes out.
4. `schedule_followup` on the card for the client's own date after delivery. The daily sweeper surfaces it.
5. A Roadmap that does not convert within 90 days is closed lost with a reason ("No response", "No longer interested", "Hired someone else", "Price too high").

## B. QuickBooks (pay on request)
Same rail as the paid findings deposit: `qbo_contact_search_customer` → `qbo_contact_create_customer` if needed → `qbo_sales_create_invoice` with one line on the `01 Assessment` service item (product_id 85), `taxable=false`, description "Entitlement Roadmap — <address>" (or Snapshot / Roadmap Plus), the standard payment-options note, and the credit line for a Snapshot ("credited to an Entitlement Roadmap ordered within 60 days"). Leave the invoice number blank (QBO sequences it). Do not enable card payment unless the client asks; then add the 3% line. Confirm the email, then `qbo_sales_send_invoice`. Paid status syncs to Productive through the weekly payment sync.

## C. Productive (after payment, or on Chris's say-so)
1. `list_companies` then `create_company` with the legal entity if absent.
2. `create_project(name="<Client short> – Entitlement Roadmap (<address>)", company_id=...)`.
3. `create_budget(project_id, company_id, name="T.O. 1 — Entitlement Roadmap (<address>)")` and `add_service(deal_id, name="Entitlement Roadmap — <address>", price=<500|2500|5000>, phase="01 Assessment", estimated_hours=<3|8.5|13>)`. Hours go in the field, never the name.
4. For a Snapshot, add the placeholder `T.O. 2 — Entitlement Roadmap (credit $500 applies within 60 days)` budget so the credit has a home.
5. Log time to the Roadmap service; the Goal 3 export counts budgets whose deal or project name contains "Roadmap".

## D. Drive
- `Sales/Proposals/[Lead Name]/Roadmap/` holds the memo (docx and PDF), the historic-aerial exhibit, the graphic schedule, and for Plus the program budget workbook (internal copy named `INTERNAL_...`, client copy without hours or roles).
- `00_Deal_Registry.md` at the lead folder root: append the rung, fee, invoice number, request and delivery dates, the one-line recommendation, the proposal that follows, and the credit.
- Screen inputs file per `new-lead-intake-screen/references/folder-routing.md` (Acres report, KML, aerials).

## E. Delivery clock
Request (paid) → screen and draft within 24 hours → senior planner review and session → send within 48 hours. The Monday brief reports Roadmaps requested, delivered, and sold; delivery time is read from the Automator stage timestamps.
