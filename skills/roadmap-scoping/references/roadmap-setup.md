# Step 1 ladder — systems sequence (portal, Automator, QuickBooks, Productive, Drive)

IDs below are from `canon/pilot.yaml` as of 2026-10-30.

## A. Portal (where the ladder is sold)
- Memberships, the trial, Screens and Scenarios reads live in the client portal. Screens and reads come from the member's monthly allowance; nothing is invoiced for them.
- The planner levels are ordered per site from the portal (`/portal/sites/<site_id>/order/<level>`; the portal's level key `planner_review` is Level 1 and `scenarios` is the Strategy Session). The order shows the list price, any credit the model's rules give, and the amount due.
- A Strategy Session order is placed at the tier the site's scale test suggests, but no invoice goes out until a planner confirms the tier in the team review queue; until then the member sees that a planner is confirming the price.
- Paid ordering sits behind the `portal_paid_levels` flag (off by default); while it is off, sell the level off-portal (section C). Once paid, the deliverable gets its due date from the feed's turnaround and the model run starts.
- The Site Walk is not orderable in the portal; sell it off a call (section C).

## B. Automator (the live card)
1. Find or create the opportunity on the Sales pipeline (`Szy5UkrLa8hJeDZpIMEv`); confirm the contact is the decision maker (`create_contact` + `update_opportunity(contact_id=...)` if not).
2. Set `source` to one canonical value: Google Ads, LinkedIn, Direct Mail, Referral, Existing Client, Website, BIA Workshop, Podcast, ABM, Meta Ads, Public Agency.
3. Stage **Roadmap Requested** when a planner level is ordered; **Roadmap Delivered** on delivery. Value = the level's fee; the Task Order 1 value replaces it when that proposal goes out.
4. `schedule_followup` on the card for the client's own date after delivery.
5. A level that does not convert within 90 days is closed lost with a reason ("No response", "No longer interested", "Hired someone else", "Price too high"), and its Roadmap marked lost (`mark_roadmap_status`).

## C. QuickBooks (pay before the work)
- **Portal orders.** The portal's checkout creates (or finds) the QuickBooks customer and an invoice for the amount due with online card and ACH on, and stores the pay link; payment arrives by webhook. Without QuickBooks configured it opens a Productive task for Chris to send the invoice by hand.
- **Off-portal orders** (a planner level sold on a call, every Site Walk): `qbo_contact_search_customer` → `qbo_contact_create_customer` if needed → `qbo_sales_create_invoice` with one line on the `01 Assessment` service item (product_id 85) until a `Q1 Screening` item exists, `taxable=false`, description "<Level> — <address>" (e.g. "Strategy Session (standard) — 1200 Elm St"). Apply any credit as a negative line (a prior Planner Review against a Strategy Session, up to $500). Site Walk expenses go on their own line at cost. Leave the invoice number blank (QBO sequences it) and add the standard payment-options note. Confirm the email, then `qbo_sales_send_invoice`. Full tool sequence and defaults: `paid-findings-report/references/paid-findings-setup.md`, section B.
- Once card billing is live, memberships, the Screen pack and the planner levels move to Stripe; QuickBooks invoicing starts at Q2 (task orders).
- Paid status syncs to Productive through the payment sync.

## D. Productive
1. Planner review work is tracked as tasks on the non-billable list **ZZ Roadmap reviews** (`2941807`) in project `983286`, assigned to Chris (`1218809`), who reviews every Screen and Scenarios order himself.
2. A client project starts at a signed task order, through `proposal-scoping` and `deal-onboarding`. Before that, a planner level sold off-portal to a lead who is already a Productive client can sit on that client's project as a Q1 Screening service: `add_service(deal_id, name="<Level> — <address>", price=<fee>, phase="Q1 Screening")`, hours (if any) in `estimated_hours`, never the name. Otherwise ask Chris before creating a project for a planner level.
3. The Map is a membership feature, never a Productive service.

## E. Drive
- `Sales/Proposals/[Lead Name]/Roadmap/` holds anything delivered outside the portal: the Strategy Session write-up (docx and PDF), the historic-aerial exhibit, the graphic schedule, the Site Walk notes and photos, and for complex sites the program budget workbook (internal copy named `INTERNAL_...`, client copy without hours or roles).
- `00_Deal_Registry.md` at the lead folder root: append the level, tier and why, fee, credit applied, invoice number, order and delivery dates, the one-line recommendation, and the task order that follows.
- Screen inputs file per `new-lead-intake-screen/references/folder-routing.md` (Acres report, KML, aerials).

## F. Delivery clock (business days from payment)
Screen 2. Planner Review 2 (1 on Team). Strategy Session 5 standard, 10 complex. Site Walk on the date agreed with the client. The Monday brief reports levels requested, delivered and sold; delivery time is read from the order dates and the Automator stage timestamps.
