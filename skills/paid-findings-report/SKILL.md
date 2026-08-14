---
name: paid-findings-report
description: "Convert a Cox Planning Solutions consult or site visit into a PAID Initial Findings Report — a flat $500/$1,000 deposit, credited toward a future scope of work — and stand the paying lead up as a client in Productive with a QBO pay-link invoice. Use when a consult/site visit has happened and the goal is to charge for the initial analysis instead of giving it away, or a lead has agreed to a paid findings report. Triggers: 'paid findings,' 'charge for the findings,' 'findings deposit,' 'site visit fee,' 'they paid the $500,' 'send the findings invoice,' 'set them up as a client,' 'bill for the initial analysis,' or a lead who agreed to pay for a report. Do NOT use for the free lead-with-value screen (use new-lead-intake-screen), for a full signed engagement's scope (use proposal-scoping), or for a change order on an existing Task Order (use change-order-scoping)."
---

# Paid Initial Findings Report

The monetized fork of the intake front end. `new-lead-intake-screen` gives the initial analysis away free to take a prospect's temperature; **this skill charges for it** — a flat, credited deposit — when Cox has already invested real time (a site visit or a substantive desktop review) and wants to cover cost before delivering findings. The deposit de-risks the free-consult leakage: even if the lead never proceeds, Cox is paid for the analysis; if they do proceed, it credits to the scope of work.

**Where it sits:** after a consult/site visit, in place of (or alongside) the free findings email. A lead who pays the deposit **becomes a client** — set them up in Productive and move their Automator opportunity accordingly. Feeds forward to `proposal-scoping` (Mode 1) for the full SOW, where the deposit is credited.

## The offer

| Tier | When | Deliverable |
|------|------|-------------|
| **$500** | Desktop review / simpler property | Short Initial Findings Report (findings + next steps + associated costs) |
| **$1,000** | Site visit / more complex property | Same, with the site-visit observations |

The fee is **credited toward the scope of work** if the lead engages Cox for the follow-on work (concept plan, pre-app, ISR, repair design, etc.). State the credit explicitly in the invoice note and the follow-up email. Deposit is a flat de-risk fee, not an hourly estimate — do not itemize hours to the client.

## Inputs to gather

- **Lead name / legal entity** (the vesting name for the invoice — e.g. "Ochi Revocable Living Trust"), and the **contact email** (from Automator — confirm it's the client, not an agent/referrer).
- **Property address / APN** and jurisdiction.
- **Which tier** ($500 vs $1,000) and **what was already done** (site visit date, desktop review).
- **What the follow-on scope will be** — so the findings report and email can name the next step the deposit credits toward.

## Stage 1 — Confirm the contact in Automator

The opportunity often comes in tagged to the referrer (an agent, a roofer, whoever called). Confirm the paying client. If the opportunity's contact isn't the client, create/link the client contact (`create_contact` → `update_opportunity(contact_id=...)`). Because they're now paying, move the opportunity out of the free-lead bucket to the appropriate client/won-adjacent stage per the current pipeline, and note the referral chain in the registry (don't lose who sent them).

## Stage 2 — Stand up the client in Productive

Run the setup in `references/paid-findings-setup.md`. In short: **company → project → two budgets → the deposit service**.

- **Company** = the legal/vesting entity.
- **Project** = `<Client short name> – <Project> (<address>)`.
- **T.O. 1 — Paid Initial Findings** budget, with one service = the findings deliverable at the tier price under the `01 Assessment` phase.
- **T.O. 2 — <follow-on work> (SOW — TBD; $ credit applies)** placeholder budget, where the deposit credits when the real scope is built.

## Stage 3 — Invoice with a pay link (QBO)

Create the QBO customer (legal entity + billing address + email), then a QBO invoice for the tier amount on the `01 Assessment` service item, card/ACH pay enabled, with a note stating the credit. **Creating does not email** — send is a separate, explicit step once the address is confirmed. Exact tool sequence in `references/paid-findings-setup.md`. When paid, it syncs back to Productive as paid via the standard QBO→Productive payment sync.

## Stage 4 — Deliver the findings report

Produce the Initial Findings Report itself — the substantive analysis the deposit bought: the constraints/observations, what they mean, recommended next steps, and the associated costs (framed as the follow-on scope the deposit credits toward). This is the paid product, so it goes deeper than a free screen would, but it is still a findings report, not the full ISR/engineering deliverable. Format in Chris's voice per `cox-document-formatting`. Include the standard non-guarantee (Cox guarantees correctly executed work, not approvals or outcomes) and the mapped-vs-field-confirmed distinction.

## Stage 5 — Seed / update the deal registry

Same live-record convention as intake Stage 7. Record: Automator opp, Productive company/project/budget ids, client entity + contact, referral chain, property/APN, the deposit amount + that it's credited, and the follow-on scope. Log the paid-findings milestone with the date.

## The follow-up email

Sent when offering the paid report (before payment) — the pivot from "happy to share initial thoughts" to "happy to deliver our findings as a report." Adapt to Chris's voice; keep the credit prominent.

> Subject: Cox Planning Solutions — Initial Findings for <property>
>
> Hi <name>,
>
> Thanks again for <the call / meeting us at the property on <date>>. We've done enough of an initial look to see there's real substance here worth putting on paper.
>
> We'd be glad to deliver our initial findings as a written report — what we're seeing, what it means, and the recommended next steps with associated costs — for a flat **$<500/1,000>**. If you decide to move forward with us on <the follow-on work>, that amount is **credited toward your scope of work**, so it's not a sunk cost — it just gets us started and covers the analysis.
>
> If that works, I'll send a quick invoice with a secure online payment link and we'll turn the report around promptly.
>
> — Chris Cox, Cox Planning Solutions

## Guardrails

- **Credit, always stated.** The deposit credits to the SOW — say so in the email and the invoice note. Never present it as a non-refundable consult fee.
- **Confirm the payer.** Invoice the entity that will sign and pay, to the confirmed email — not the referrer.
- **Internal vs. client-facing.** The tier price is flat; never itemize hours to the client. Hours basis, if tracked, goes in the Productive service's estimated-hours field.
- **Send is explicit.** Draft the invoice, confirm the email, then send — never auto-send.
- **No guaranteed outcomes.** Findings are analysis, not secured approvals or valuations.
- **Paying = client.** Once the deposit is invoiced, they leave the free-lead pipeline; the two-budget Productive setup carries the credit forward to `proposal-scoping`.
