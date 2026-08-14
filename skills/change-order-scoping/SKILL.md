---
name: change-order-scoping
description: "Scope a change order to an active Cox Planning Solutions Task Order (Mode 2). Use when an existing client wants added scope, a scope revision, or a budget adjustment to an engagement already under a signed Task Order, and the task is to document only what is changing and produce the change-order handoff memo. Triggers: 'change order,' 'add scope,' 'additional scope,' 'scope revision,' 'modify the task order,' 'budget adjustment,' 'expand the engagement,' or a reference to an existing Task Order plus new work. Do NOT use for a brand-new engagement or agreement (use proposal-scoping), for the initial unpaid lead screen (use new-lead-intake-screen), or for onboarding (use project-setup-workbook-asana-sync)."
---

# Change Order Scoping (Mode 2)

A change order modifies an existing Task Order rather than creating a new agreement. The discipline is the same as proposal scoping, but document **only what is changing**.

## Before doing anything

| Step | Read first |
|------|-----------|
| Structuring the change order and its memo | `references/change-order-format.md` |
| Rates, fee backup, billing types | The `billing-and-fees.md` reference in the proposal-scoping skill — same rate table, cost-floor discipline, and billing types apply |
| Estimating hours per deliverable | The `estimating-basis.md` reference in the proposal-scoping skill — empirical per-deliverable hour ranges from real Cox budgets |

## How it differs from a new proposal

- Reference the existing Task Order number and original proposal; do **not** re-attach the Standard Agreement — it is already in place. Reference its original date.
- Document only added scope, removed scope, and the fee adjustment. Do not restate the original project.
- The introduction describes **what changed and why**, not the original project context.
- Payment is typically a **single milestone** (upon signature, or upon the added deliverable), not 40/40/20.
- Build the same internal fee backup for the changed scope (hours × cost vs. proposed adjustment, against the real cost floor), and have Chris approve the margin before finalizing.

## Sequence

1. Confirm the active Task Order being modified and its current fee/NTE.
2. Define the change: added deliverables (with `Phase.Deliverable` codes), removed deliverables, and the net fee delta.
3. **Ground the estimate in actuals, then in the basis (the estimate engine).**
   - Run `cox-productive-tools/co_actuals.py <project_id> --rate <blended>` to pull approved-budget
     vs. worked hours per deliverable. Separate **already-incurred overage** (worked > budget — sunk
     revision cost not yet recovered) from **forward work** (not-started deliverables — genuinely
     incremental). A change order recovers the *incremental hours going forward*, informed by the
     overage, not equal to it. Overall-under-budget projects can still justify a CO — the trigger is
     the scope change (e.g. a project-boundary revision), and unstarted deliverables will consume
     their budgets *plus* the revision increment.
   - Cross-check each line against `estimating-basis.md` (empirical per-deliverable medians/ranges).
   - **Chris and Kristin supply the anticipated additional hours per line.** Do not invent them;
     present the actuals + basis as the frame and hold for their input.
4. Build the fee backup for the changed scope only (hours × rate vs. proposed fee, against the real
   cost floor, plus subs at +15% and 8% PM go-forward). Show Chris; he approves the margin.
5. Set the single-milestone payment trigger.
6. **Write the priced change as services on the Productive change-order deal** — one `add_service`
   per changed deliverable (`add_service(deal_id, name="<Phase.Deliverable name> — Revisions",
   price=<fee $>, phase="<NN Phase>")`), setting `estimated_time` to the agreed hours so the deal
   carries the hours basis, not just a dollar amount. **Hours belong in the estimated-hours field,
   never typed into the description.** Rates come from each person's rate card — never add people as
   service lines. See the proposal-scoping `references/proposal-generation.md` for the exact call
   shapes and phase→service-type map.
7. Produce the change-order handoff memo per `references/change-order-format.md`, with the Change Order Summary section ahead of the scope.

## Guardrails

Same as proposal scoping: Cox not "CPS"; brand standards; no guaranteed outcomes; specific scope language; fee backup stays internal.
