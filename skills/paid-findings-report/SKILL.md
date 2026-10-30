---
name: paid-findings-report
description: "Charge for a Cox Planning Solutions consult or site visit by selling the matching Step 1 planner level off the portal — a Planner Review ($500, $400 for members; credited up to $500 into a Strategy Session), a Strategy Session ($1,000 standard / $2,500 complex by the scale test), or a Site Walk ($1,000 local / $2,500 remote or large, plus expenses; credited into 2A Field Verification if signed within 90 days) — invoiced with a QuickBooks pay link, with the paying lead recorded on the Automator card and the deal registry. Prices are read from the model's pricing feed (canon/pilot.yaml). Use when a call or site visit has happened and the goal is to charge for the analysis instead of giving it away. Triggers: 'paid findings,' 'charge for the findings,' 'findings deposit,' 'site visit fee,' 'charge for the site visit,' 'send the findings invoice,' 'bill for the initial analysis,' 'invoice the planner review,' 'invoice the site walk,' or a lead who agreed to pay for a planner's read. Do NOT use for the free lead-with-value screen (use new-lead-intake-screen), for choosing between the levels or delivering them (use roadmap-scoping), for a signed engagement's scope (use proposal-scoping), or for a change order (use change-order-scoping)."
---

# Paid findings: selling a planner level off a call or site visit

The monetized fork of the intake front end. `new-lead-intake-screen` gives the initial analysis away free to take a prospect's temperature; **this skill charges for it** when Cox has already invested real time (a call with a substantive desktop look, or a visit to the site) and the lead wants a planner's written read before committing to a task order.

**Since 2026-10-30 there is no separate findings product.** The $500 / $1,000 credited findings deposit is retired. What a lead buys is one of the Step 1 planner levels, at the price and credit the model's pricing feed (`canon/pilot.yaml`) carries on the day. `roadmap-scoping` owns the ladder, the scale test and the delivery; this skill covers the off-portal sale: confirming the payer, the offer email, the QuickBooks pay-link invoice, and the record.

## The offer (as of 2026-10-30; confirm against the feed before quoting)

| Situation | Level | Price | Credit |
|-----------|-------|-------|--------|
| A desktop look the lead wants checked and signed | **Planner Review** (level 1) | $500; $400 for members | Up to $500 off a Strategy Session on the same site |
| The lead is weighing paths and wants the next studies priced | **Strategy Session** (level 2) | $1,000 standard or $2,500 complex, by the scale test; Chris confirms the tier before the invoice | — |
| A visit to the site, or one the lead wants | **Site Walk** (level 3) | $1,000 local (within about an hour's drive of Sacramento) or $2,500 remote or large, plus expenses | In full toward 2A Field Verification if the task order is signed within 90 days |

A credit is good for 12 months after the level is delivered. The Strategy Session and Site Walk are never discounted. A lead with portal access can order the Planner Review or Strategy Session there instead (roadmap-scoping, `references/roadmap-setup.md` section A); use this skill when the sale happens on a call or at the site, or for any Site Walk.

**Legacy deposits.** A findings deposit collected before 2026-10-30 (e.g. the Ochi pilot) is still honored as the credit its invoice stated; `deal-onboarding` nets it against the down payment. Do not sell new ones.

## Inputs to gather

- **Lead name / legal entity** (the vesting name for the invoice, e.g. "Ochi Revocable Living Trust") and the **contact email** (from Automator; confirm it is the client, not an agent or referrer).
- **Property address / APN** and jurisdiction.
- **Which level**, and for a Strategy Session the scale-test facts (roadmap-scoping `references/scale-test-and-pricing.md`); for a Site Walk, local or remote and the expected expenses.
- **What was already done** (call date, site visit date, desktop review) and **what the task order after it would be**.

## Stage 1 — Confirm the contact in Automator

The opportunity often comes in tagged to the referrer (an agent, a roofer, whoever called). Confirm the paying client. If the opportunity's contact isn't the client, create and link the client contact (`create_contact` → `update_opportunity(contact_id=...)`). Move the card to **Roadmap Requested** with the level's fee as its value, and note the referral chain in the registry.

## Stage 2 — The draft Roadmap

If the prospect has no draft Roadmap yet, start one now (`start_draft_roadmap`; new-lead-intake-screen 5.5). The planner level is delivered from it, and Task Order 1 is built on its deficiencies.

## Stage 3 — Invoice with a pay link (QBO)

Create or find the QBO customer (legal entity, billing address, email), then a QBO invoice for the level on the `01 Assessment` service item, with any credit as a negative line and Site Walk expenses on their own line. For a Strategy Session, invoice only after Chris confirms the tier. **Creating does not email**; send is a separate, explicit step once the address is confirmed. Exact tool sequence in `references/paid-findings-setup.md`. Paid status syncs back to Productive through the payment sync.

No Productive project is created for a planner level; the project starts at a signed task order. If the lead is already a Productive client, the level can sit on their existing project as a `Q1 Screening` service (`references/paid-findings-setup.md`, section A).

## Stage 4 — Deliver

Payment comes first. Deliver the level per `roadmap-scoping` Stage 3 to 5: the Planner Review signed, the Strategy Session write-up and session, or the Site Walk notes. Every deliverable carries the standard non-guarantee (Cox guarantees correctly executed work, not approvals, timelines or valuations) and the mapped-versus-field-confirmed distinction.

## Stage 5 — Update the deal registry

Same live-record convention as intake Stage 7. Record: Automator opp, level and tier, fee, any credit applied, the QBO invoice number, client entity and contact, referral chain, property/APN, and the task order that follows. Log the date it was ordered, paid and delivered.

## The offer email

Sent when offering the paid level (before payment). Adapt to Chris's voice; keep the credit and the price source accurate.

> Subject: Cox Planning Solutions — <property>
>
> Hi <name>,
>
> Thanks again for <the call / meeting us at the property on <date>>. We've done enough of an initial look to see there's real substance here worth putting on paper.
>
> The next step we'd suggest is a <Planner Review / Strategy Session / Site Walk>: <one sentence on what it gives them, from the feed's description>. It is <price as the feed phrases it>. <The credit sentence, if any: "If you go on to a Strategy Session, it comes off that fee, up to $500." / "If you go ahead with field verification within 90 days, the full fee is credited toward it.">
>
> If that works, I'll send an invoice with a secure online payment link, and we'll start as soon as it's paid.
>
> Chris Cox, Cox Planning Solutions

## Guardrails

- **Prices from the feed.** Quote `canon/pilot.yaml` as of the day; never a remembered figure, never a discount on the Strategy Session or Site Walk.
- **Credits stated exactly as the feed gives them**, in the email and the invoice note. Never promise a credit the feed does not carry.
- **Confirm the payer.** Invoice the entity that will sign and pay, to the confirmed email, not the referrer.
- **Internal vs. client-facing.** Never itemize hours to the client.
- **Send is explicit.** Draft the invoice, confirm the email, then send; never auto-send.
- **No guaranteed outcomes.** Analysis, not secured approvals, timelines or valuations. Ranges, not points.
- **House style.** "Cox" or "Cox Planning Solutions", never "CPS"; no exclamation marks.
