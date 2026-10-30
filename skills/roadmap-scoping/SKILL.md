---
name: roadmap-scoping
description: "Sell and deliver Cox Planning Solutions' Step 1 (Q1 Screening) ladder and keep every prospect's draft Roadmap (Mode 3): the portal membership and its 30-day trial, the automated Screen (1A) and the automated Scenarios read (1B) that come with it, and the paid planner levels (1C) — Planner Review, Strategy Session (priced by the scale test) and Site Walk — with their credits, turnaround, the scale test, the Automator Roadmap stages, payment, and the hand-off to Task Order 1. Use when a prospect asks for a roadmap or a planner's read of a site, when a Screen or Scenarios read comes back and the next step is a planner level, when a planner level is ordered or needs delivering, or when the draft Roadmap has to be started before a first task order. Triggers: 'roadmap,' 'entitlement roadmap,' 'build a roadmap for [address],' 'they want the roadmap,' 'draft roadmap,' 'strategy session,' 'planner review,' 'site walk,' 'scale test,' 'standard or complex,' 'membership,' 'trial,' 'screen,' 'scenarios read,' 'what would it take to build on [parcel],' 'roadmap requested,' 'deliver the roadmap.' Do NOT use for the free intake screen alone (use new-lead-intake-screen), for a task order proposal at Step 2 or later (use proposal-scoping), or for a change order (use change-order-scoping)."
---

# Step 1 ladder and the Roadmap (Mode 3)

Step 1 of the Development Decision Ladder is Q1 Screening: should I proceed? Cox sells it as a membership with automated reads and paid planner work on top. Projects start later, at a signed task order (Step 2 and up, beginning with Q2 Underwrite). Every prospect also gets a draft Roadmap in the Cox Entitlement Model before Task Order 1, whether or not they buy anything on this ladder.

**The source of truth for every price, allowance, credit and turnaround is the model's pricing feed, `canon/pilot.yaml` in the Cox Entitlement Model** (published to the portal as `roadmaps/_pricing.json`; phrasing in `engine/offers.py`). The figures below are as of 2026-10-30. If a figure here and the feed disagree, the feed wins: quote the feed and tell Chris this skill needs updating. Never quote a price the feed does not carry. Full detail and the scale test: `references/scale-test-and-pricing.md`.

## The ladder (as of 2026-10-30)

| Step | What it is | Price | Turnaround |
|------|-----------|-------|------------|
| **Membership** | The portal: the Cox map app for the tier's coverage, saved projects, and monthly allowances of Screens and Scenarios reads | Starter $49, Pro $149, Team $399 a month; a year costs ten months; extra Screens 25 for $150; Pro is free while a Cox task order is active | — |
| **Trial** | 30 days, 3 Screens and 1 Scenarios read, no card. Each evaluation form the member completes unlocks another 30 days, up to 2 (90 days at most) | free | — |
| **1A Screen** (automated) | An automated read of one parcel against the member's project type: what the rules allow, whether the lot fits, which state laws may apply, what to check, with a source on every finding. Labeled Unreviewed | from the membership allowance | 2 business days |
| **1B Scenarios** (automated) | The automated Scenarios read, built on a Screen of the same parcel: the development scenarios, a narrative, the schedule, the tasks and the budget (or "Budget pending" with what it needs). Approval outlook as a band only, never a percentage. Ends with "What would settle it". Labeled Unreviewed | from the membership allowance | — |
| **1C Level 1: Planner Review** | A planner checks the Screen and Scenarios read, confirms the facts from up to 5 of the client's documents (100 pages), and signs the Screen | $500; $400 for members | 2 business days (1 on Team) |
| **1C Level 2: Strategy Session** | A planner ranks the paths, prices the next studies and walks the client through them in a session. Every document on the site is read in full | $1,000 standard or $2,500 complex, by the scale test; a planner confirms the tier before payment; no member price | 5 business days standard, 10 complex |
| **1C Level 3: Site Walk** | A planner walks the site with the client and notes what needs field verification | $1,000 local (within about an hour's drive of Sacramento) or $2,500 remote or large, plus expenses; no member price | scheduled with the client |

**Credits.** A paid Planner Review comes off a Strategy Session on the same site, up to $500 (Chris confirmed keeping it, 2026-10-30). A Site Walk is credited in full toward 2A Field Verification if the task order is signed within 90 days. A completed level's credit is good for 12 months after delivery. Nothing else credits forward, and the larger planner levels are never discounted.

**Names.** "Scenarios" is the automated 1B read and nothing else. The paid Level 2 is the "Strategy Session"; never call it Scenarios, and never call the automated read a Strategy Session. (The portal's internal order key `scenarios` is the Strategy Session; that is a code name, not client language.) The Map is a membership feature, not a step, and no separate Map license is sold. The old rungs (Step 1a Screening $500, Roadmap $2,500, Roadmap Plus $5,000, the 12 Oct per-order levels, the credited findings deposit) are retired; do not offer them.

**What is on sale today.** The membership is decided (`on_sale: true`) but opens only when card billing is live (`billing.card_live` was false on 2026-10-30). Until then the trial is how prospects use the portal, and the planner levels are invoiced through QuickBooks pay links (`references/roadmap-setup.md`). Once card billing is live, memberships, the Screen pack and the planner levels are charged by card through Stripe; from Q2 on, work is quoted and invoiced through QuickBooks as task orders are now. Sales tax applies to memberships and the Screen pack from 1 Jan 2027; planner levels and task orders are services and are not taxed.

**Who gets offered what** (`personas` in the feed): builders and developers are steered to Pro and the Strategy Session; investors, lenders, owners, operators and brokers to the Planner Review (owners and operators see "Talk to a planner" first); architects, engineers and consultants are partners, not upsells; public agencies get no sales follow-up.

## A draft Roadmap for every prospect (Chris, 2026-10-25)

Every prospect gets a draft Roadmap in the Cox Entitlement Model before Task Order 1 goes out, whether or not they buy a planner level. It is Cox's own working record and stays internal until a planner releases it.

1. Start it with the cox-productive connector's **`start_draft_roadmap`** (new-lead-intake-screen 5.5 has the fields). If the site already has a project (`list_projects` on the Cox Model connector), add to it with `propose_update` instead.
2. Lead it with the **deficiencies**: the gaps between what the prospect wants and what the code requires, each with every way to resolve it, its approval, who decides, time and cost ranges and an outlook band (strong, moderate, uncertain or difficult; never a percentage).
3. The deficiencies guide Task Order 1 (each TO1 deliverable answers a deficiency or an assumption), give the discussion points for the client call, and set up the later task orders. The dashboard will not release a new client's first task order without a Roadmap (proposal-scoping A2 and G).
4. A lost job keeps its record: `mark_roadmap_status(key, "lost", reason, evidence, date)`. It is still a data point for the model and a pass over the regulations.

## Inputs to gather

In one pass, not stage by stage:

- **Client name and legal entity**, contact email and phone, and whether the contact is the owner, a buyer, a broker or an agent (write to the decision maker).
- **Property address and/or APN**, jurisdiction, approximate acreage, number of parcels and whether they are adjacent and in one ownership.
- **What they want to do** with it, or the decision they face (build, buy, sell entitled, hold).
- **What they already have**: a Screen or Scenarios read in the portal, a survey, prior studies, a concept plan, agency correspondence.
- **The scale-test facts** (`references/scale-test-and-pricing.md`) if a Strategy Session is in play.

Proceed with an address alone if that is all there is; flag the gaps as "what would firm this up."

## Stage 1 — Place the prospect on the ladder

1. Find or create the Automator opportunity (`list_opportunities` / `create_opportunity`), confirm the contact is the decision maker, and set `source` to a canonical name (Google Ads, LinkedIn, Direct Mail, Referral, Existing Client, Website, BIA Workshop, Podcast, ABM, Meta Ads, Public Agency). Move it to **Roadmap Requested** when a planner level is ordered.
2. Decide the offer. A prospect with no Screen yet starts with the trial. A Screen or Scenarios read that needs a planner's signature is a Planner Review. A prospect weighing paths and the next studies is a Strategy Session. Anyone who needs eyes on the ground before committing to field work is a Site Walk. A prospect ready to commit goes straight to Task Order 1 (proposal-scoping), built on the draft Roadmap.
3. For a Strategy Session, run the scale test and record the tier and the triggers in the registry. A planner (Chris) confirms the tier before any invoice goes out; an override below the test's tier needs a written reason. Never downgrade a complex site to win the sale.
4. Scaffold `Sales/Proposals/[Lead Name]/` if it does not exist (new-lead-intake-screen Stage 1).
5. Payment comes before the work (`references/roadmap-setup.md`): the portal's checkout for an order placed there, or a QuickBooks pay-link invoice for one sold off a call or a site visit. Apply the credits above.

## Stage 2 — Screen the property (the model input)

The portal's Screen is the starting point when there is one. For planner work, run the new-lead-intake-screen Stage 3 to 5 sequence comprehensively: Acres report and KML filed, zoning and General Plan, development standards, environmental constraints with the live NWI and CARI queries, and the regulatory lever scan. Add, for a Strategy Session:

- **Historic aerials.** Ten years or more of the Google Earth record where available: site condition, disturbance, ponding in wet-season frames, structures that came and went, trees to check for nesting.
- **The agency path.** The discretionary approvals the stated use needs in this jurisdiction (rezone, use permit, design review, subdivision map, density bonus, CEQA pathway), the approval body for each, and whether a federal or state resource permit enters the picture.
- **Market read, only as far as the write-up needs it.** Enough to say which product form pencils. Cox is not the appraiser; say so.

Everything traces to the screen or the register. A figure without a source is an assumption and is labeled as one.

## Stage 3 — Do the planner work

- **Planner Review.** Check every finding in the Screen and Scenarios read against the record and the register, correct what the data got wrong (log corrections to the correction register), confirm the facts from the client's documents (up to 5, 100 pages), and sign the Screen. The signed Screen names the approvals in order, cost and schedule ranges with their assumptions, and the questions to answer next.
- **Strategy Session.** Write it up to `references/roadmap-memo-template.md` in Chris's voice per `cox-document-formatting`: the paths ranked, the next studies priced (from the fee engine where it has inputs, otherwise named and left to the task order), then hold the session, capture the client's timeline, capital and appetite, and revise once if the session changed the recommendation. Complex sites add the items in the template's complex-tier section.
- **Site Walk.** Walk the site with the client and record what needs field verification, by location, with photos. That list becomes the scope of 2A Field Verification, which the Site Walk fee is credited toward if signed within 90 days.

Standing rules for all three:

- **Bottom line first**, in words a client can repeat to a partner.
- **Ranges, not points**, for anything unresolved, each with the one thing that would firm it up. Where the inputs that set a cost do not exist yet, name the drivers instead of inventing a bracket.
- **Mapped is not field-confirmed.** Every constraint statement says which it is.
- **No hours, rates, roles, margin or vendor names** in anything the client sees.
- **No guarantees** of approval, timeline or valuation; the non-guarantee from the template's Terms goes in verbatim.
- **The next step is a task order**, named: usually Task Order 1 at Q2 Underwrite (2A Field Verification, 2B Site Plan and Alternatives), built from the draft Roadmap's deficiencies. Permits are Q4 and are never the next conversation.

## Stage 4 — Planner review gate

Work through `references/qc-checklist.md` with Chris before anything goes to the client; the checklist is the gate. Chris reviews every Screen and Scenarios order himself (`reviewers` in the feed).

## Stage 5 — Deliver, record and convert

1. Deliver in the portal for an order placed there; otherwise save the PDF in `Sales/Proposals/[Lead Name]/Roadmap/` with the Cox cover and footer, and send it with a short cover email in Chris's voice that names the next step. Send is explicit: draft, review, confirm the address, then send.
2. Move the Automator opportunity to **Roadmap Delivered**, set the value to the level's fee, and schedule the follow-up for the date the client committed to (`schedule_followup`), not a drip interval.
3. Append the registry (`00_Deal_Registry.md`): the level, tier and why, fee, credit applied, invoice number, delivery date, the one-line recommendation, and the task order that follows.
4. Hand to `proposal-scoping` (Mode 1) for Task Order 1. The write-up's "what could change it" section and the Roadmap's deficiencies are that task order's scope.

## What this skill measures

Planner levels sold per month, the 90-day conversion of planner levels to a signed task order, and delivery time against the feed's turnaround. Every level that does not become a task order within 90 days gets a lost reason on the opportunity, and its Roadmap is marked lost.

## Guardrails

- **Prices come from the feed.** Quote `canon/pilot.yaml` as of the day; never a remembered or invented figure, never a discount on the Strategy Session or Site Walk.
- **Regulations: register first, then official sources only** (Chris, 2026-10-27).
  - Check the Cox Entitlement Model register first (`register_search`, `register_record`, `project_register`).
  - If the register doesn't cover a rule, read only the official code: American Legal, eCode360, Municode, the jurisdiction's own site, leginfo for State law, eCFR and the Federal Register for federal law. Then file a register request (`propose_update` with kind `record_needed`).
  - An unofficial copy (gocodebook, casetext, summaries) may help locate a section, but never cite one. Its section numbers can be out of date.
  - If the official page won't load, mark the finding unverified rather than using a mirror.
- **Step 1 is a desktop planning product, not a scope of work or a fee proposal**, except the Site Walk, which is a field observation and still not a delineation or a survey.
- **No Productive project before payment** or Chris's exception. The screen and the draft Roadmap can proceed; the project starts at a signed task order.
- **Owner identity and site information stay inside the engagement.** Marketing reuse is anonymized and only with the client's consent.
- **Anything touching the Clean Water Act 404(f) agricultural exemption, or language that could read as concealing a resource from an agency, stops and goes to Chris.**
- **No fabricated citations.** If the code section or fee schedule is not in hand, mark `[FACT NEEDED]`.
- **House style.** "Cox" or "Cox Planning Solutions", never "CPS"; ranges, not point estimates; no exclamation marks.
