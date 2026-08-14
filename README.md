# Cox Proposals

Sales and proposals automation for Cox Planning Solutions, packaged for Claude Cowork. Covers the pipeline from a lead landing through scoping a signed-ready proposal and handling change orders.

## Skills

### new-lead-intake-screen (pre-handoff)
From a lead landing to the findings email: scaffolds the proposals folder, files the transcript and intake documents, screens zoning/General Plan/development standards/environmental constraints and the California regulatory levers, and drafts a lead-with-value findings email with a rough scope and budget range. Trigger: "new lead, [name], [address or APN]."

### proposal-scoping (v0.4.0: writes services to the Productive deal + generates the branded proposal instead of a handoff memo) (Mode 1)
Once a prospect bites: builds the scope of work, the internal fee backup and budget (against the real cost floor, not MSRP), timeline, and payment terms, then produces the handoff memo the coordinator turns into the Automator document. Handles three billing types — fixed fee, T&M NTE, and subscription/productized. Trigger: "scope a proposal," "fee backup," "build a proposal for [client]."

### change-order-scoping (Mode 2)
Modifies an active Task Order: documents only what changed, builds the fee backup for the changed scope, sets a single-milestone payment, and produces the change-order handoff memo. References the existing agreement rather than re-attaching it. Trigger: "change order," "add scope to [project]."

## Composes with
- Upstream: the intake screen feeds the rough scope and budget into proposal-scoping.
- Downstream: `project-setup-workbook-asana-sync` picks up onboarding once a proposal is won.
- `cox-document-formatting` for any client-facing language.

## Notes
- The firm is "Cox," never "CPS" — including internal project-number prefixes.
- Fee backup, hours, and cost floors stay internal and never appear in client-facing material.
- Subscription/productized pricing is the newest billing type (see proposal-scoping `billing-and-fees.md`); it is a candidate Operations Manual addition to the Billing Types section.
- The regulatory-levers logic is shared with the screening-tool app build — keep both pointed at one canonical source so a legal-reform update fixes both.
