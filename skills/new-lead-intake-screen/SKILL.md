---
name: new-lead-intake-screen
description: "Run the Cox Planning Solutions pre-handoff intake and property screen for a new lead. Use when a new prospect calls or books an appointment and the goal is to set up the proposals folder, file the call transcript and intake documents, screen the property for zoning/general plan, development standards, environmental constraints, and California regulatory levers, and draft a lead-with-value findings email with a proposed scope and approximate budget. Triggers include: 'new lead,' 'got a call from,' 'booked an appointment with,' 'intake a lead,' 'screen a property,' 'property screen,' 'constraints screen,' 'scope a new prospect,' 'set up a proposals folder,' or a prospect name plus a property address or APN. Do NOT use for active client projects already under a Task Order (use project-setup-workbook-asana-sync), or for the full paid regulatory analysis inside a Step 1 ISR (this skill produces the initial strategic screen only)."
---

# New Lead Intake and Property Screen

This skill covers the unpaid front end of the Cox sales pipeline — everything from a lead landing to the findings email that takes the prospect's temperature, before any handoff to proposal assembly. It pairs with `cox-document-formatting` (for the findings email and any scope language), seeds the deal registry (Stage 7), and feeds forward to `proposal-scoping` (Mode 1) and, once won, `project-setup-workbook-asana-sync`.

The operating principle: because the screen is automated, run it **comprehensively by default**. The marginal cost of a thorough screen is near zero, and a substantive initial analysis — strategy and constraints other firms gate behind a paid engagement — is Cox's primary acquisition advantage. Do not ration depth by perceived deal quality unless the user explicitly asks for a quick screen.

## Before doing anything

Read the reference file that matches the step:

| Step | Read first |
|------|-----------|
| Filing the transcript or any intake document | `references/folder-routing.md` |
| Screening environmental and topographic constraints | `references/constraint-sources.md` |
| Scanning for regulatory levers / reform-law pathways | `references/ca-regulatory-levers.md` |
| Seeding the deal's live record (Stage 7) | `references/deal-registry.md` |

Read more than one — Stage 5 spans constraints and levers together.

## Inputs to gather

Confirm or ask for, in one pass (do not interrogate stage by stage):

- **Lead name** as it should appear on the folder (first name as given, not the full vesting name).
- **Property address and/or APN**, and the **jurisdiction** (city if incorporated, otherwise county).
- **What the prospect wants to accomplish** — what they want to build or the decision they face.
- **Source of contact** — booked appointment (transcript in Granola) or phone/email/text (captured per contact in Automator). For now the user pastes the transcript manually; treat Automator per-contact capture as a future automation, not a current input.

Proceed with whatever is available and flag gaps inline. An address or APN is enough to start the screen; the rest can follow.

## Stage 1 — Scaffold the proposals folder

Create `Sales/Proposals/[Lead Name]/` with the standard Cox phase tree. See `references/folder-routing.md` for the exact structure. This is deterministic — create the full tree even if only the transcript exists yet, so every later document has a home.

## Stage 2 — File the correspondence

Place the call/appointment transcript in `00 Project Management/Correspondence`. The user pastes the transcript text; save it as a dated file (`YYYY-MM-DD_[Lead]_Intake_Transcript`). When Automator per-contact capture is wired, this stage pulls calls, emails, and texts for the contact automatically — until then, manual.

## Stage 3 — Ingest the Acres report and KML

The user downloads the **property report and KML together from Acres** and saves the report to `01 Assessment/Property Reports` and the KML to `01 Assessment/Figures and Maps`. Read the Acres report — it already carries **topography and FEMA flood data**, so pull those from it rather than re-deriving them. Acres has no API; report generation stays manual. Once saved, treat the report as the spine of the constraints screen and cross-reference Google Earth aerials where the user has flagged better imagery.

## Stage 4 — Route any additional documents

When the prospect sends background material, file each item by type using the routing table in `references/folder-routing.md`. If a document has no clear home, place it in `Client Intake` for later triage and note it — do not force-fit. Ask before filing genuinely ambiguous items.

## Stage 5 — Property and regulatory screen

This is the core. Work in order; each layer informs the next.

### 5.1 Zoning and General Plan — do this first

Identify the **zoning designation** and the **General Plan land use designation** for the parcel. These gate everything that follows — density, use, and which regulatory levers are even available. Pull from the jurisdiction's parcel viewer, zoning map, and General Plan land use map; see `references/constraint-sources.md`. State both designations and what each permits.

### 5.2 Development standards

From the zoning code for that designation, capture the standards that drive yield and form: permitted/conditional uses, density or units-per-acre, FAR, height, setbacks, lot coverage, and parking. Note where standards constrain the prospect's stated intent.

### 5.3 Environmental constraints

Screen the parcel against:
- **Topography** — from the Acres report (slope, contours).
- **FEMA flood** — from the Acres report (zone, panel); cross-check the public NFHL if the report is unclear.
- **Wetlands (NWI)** and **aquatic resources (CARI)** — REQUIRED live query, not optional. Derive the parcel centroid (geocode the address or use the APN), build a small envelope around it, and query the USFWS NWI and SFEI CARI services per the recipe in `references/constraint-sources.md`. Record, for each: feature count in the envelope, feature type/name where the service returns it, and the centroid + envelope coordinates and service URLs used. A non-zero count is a screening flag — report it, do not skip it.
- **Habitat / HCP-NCCP overlays** where the jurisdiction has them.

Report what is mapped versus what would require field verification. Do not state mapped or client-reported conditions as field-confirmed findings.

**Maps.** Figure-quality NWI and CARI map exhibits (parcel boundary over aerial with features overlaid) are a paid ISR deliverable, not a free-screen output. In the screen, record presence, counts, the identified features, the centroid/envelope, and the service URLs so the ISR figure step is teed up. Generate exhibits at ISR.

### 5.4 Regulatory lever scan — initial and strategic

Using the zoning and General Plan base from 5.1, scan for **California reform-law pathways that could unlock development potential** — density bonus, ADU/JADU stacking, SB 9, SB 330 protections, ministerial streamlining, and others. See `references/ca-regulatory-levers.md` for trigger conditions, citations, and disqualifiers.

This is an **initial flag, not the paid analysis.** The defensible lever analysis is a Step 1b ISR deliverable. Here, the purpose is to surface which pathways plausibly apply so they can be positioned as scope in the findings email — the lever flag is what sells the ISR. Frame levers as candidates to confirm, cite the governing code, and note the obvious disqualifiers. Do not overstate yield or present a pathway as secured.

## Stage 6 — Draft the findings email

Produce a lead-with-value email that takes the prospect's temperature. Three parts:

1. **Initial findings** — what the screen shows: zoning/GP, the standards that matter, the live constraints, and the regulatory levers worth pursuing. Substantive but not exhaustive; this demonstrates capability without delivering the paid product.
2. **Proposed scope of work** — the engagement that turns the screen into a defensible product, usually the Step 1b Initial Screening Report ($2,500 standard; $1,000 only for small or simple parcels), and where relevant a Step 1a screening-tool subscription. Route the Stage 5.4 lever flags into scope line items so the strategy reads as the reason to engage.
3. **Approximate budget** — a range, not a fixed number, consistent with the Mode 1 rate table and hours benchmarks. Show the range only; the fee backup is internal and never appears in client-facing material.

Write in Chris's voice per `cox-document-formatting` (first-person plural, plain language, no buzzwords, no exclamation marks). Include the standard non-guarantee: Cox guarantees correctly executed work, not permit approvals or valuation outcomes. Answer follow-up questions with enough to keep the conversation moving, not the full analysis.

## Stage 7 — Seed the deal registry

The deal is a **live record** the Sales Team project maintains from here through signature, the
same way the client project registry works post-onboarding. Intake seeds it; `proposal-scoping`
keeps it current. Full convention in `references/deal-registry.md`. Two seeds:

1. **Point the Automator card at the client.** The opportunity often comes in tagged to whoever
   made contact — sometimes an agent or referrer, not the client who will sign and pay. Confirm
   the contact. If it is not the client, create the client contact and link it:
   `create_contact(...)` → `update_opportunity(contact_id=<new id>)` (Automator MCP). Keep the
   agent/referrer noted in the registry, not as the opportunity's primary contact. If the deal
   has no value yet, leave it — scoping sets it.
2. **Create `00_Deal_Registry.md`** at the top of `Sales/Proposals/[Lead Name]/` (Google Drive
   MCP `create_file`) so every later step has one place to read state and append to:

   ```
   # Deal Registry — <Client legal entity> (<short project / property>)

   - Automator opp:   ghl:<oppid>
   - Productive deal: <deal id, if the sync has created it yet — else "pending sync">
   - Client entity:   <legal entity>      Contact: <name / email / phone>
   - Agent / referrer: <name>             (if any)
   - Property / APN:  <address> / <APN>
   - DDL step:        <1a | 1b | 2 | …>
   - Current stage:   <pipeline stage>     Value: <range or TBD>

   ## Log
   - YYYY-MM-DD  intake screened — <one line: what the go/no-go looked like>
   ```

If a `00_Deal_Registry.md` already exists (a re-run or a returning lead), **read it first** and
append rather than overwrite.

## Guardrails

- **Comprehensive by default.** Only throttle to a quick screen if the user asks.
- **Internal vs. client-facing.** Fee backup, hours, and cost floors stay internal. The email shows clean findings, scope, and a budget range.
- **No guaranteed outcomes.** Never state or imply approval, timeline certainty, or valuation.
- **Citations to confirmed facts.** Distinguish mapped/client-reported conditions from field-confirmed findings. Levers are candidates pending the paid analysis.
- **Hand-offs.** Once the prospect bites and a proposal is warranted, the scope and budget here feed `proposal-scoping` (Mode 1) via the deal registry seeded in Stage 7; once won, `project-setup-workbook-asana-sync` takes over onboarding. The VIP / public-agency exception (no down payment, monthly invoicing — e.g., Panattoni, public agencies) is handled downstream at onboarding, not here.
