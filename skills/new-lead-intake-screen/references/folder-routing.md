# Folder Structure and Document Routing

The standard Cox proposals folder tree, created under `Sales/Proposals/[Lead Name]/` on a new lead. Numbering matches the six-phase work hierarchy (00 Project Management through 05 Compliance), with Legal and Client Intake as cross-phase folders.

## Folder tree

```
Sales/Proposals/[Lead Name]/
├── 00 Project Management
│   └── Correspondence ............ transcripts, emails, texts, call logs
├── 01 Assessment
│   ├── Property Reports .......... Acres report
│   ├── Figures and Maps .......... KML, aerials, exhibits
│   └── (background studies filed loose in 01 Assessment)
├── 02 Design
├── 03 Planning
├── 04 Permits
│   └── [Agency Name] ............. one subfolder per agency
├── 05 Compliance
├── Legal
└── Client Intake
```

Create the full tree even if only the transcript exists yet. `04 Permits` agency subfolders are created on demand, named for the agency (e.g., `City of Sacramento`, `USACE`, `RWQCB`, `CDFW`).

## Routing table

| Document type | Destination |
|---|---|
| Call/appointment transcript, email, text, call log | `00 Project Management/Correspondence` |
| Acres property report | `01 Assessment/Property Reports` |
| KML, aerial imagery, exhibits, figures | `01 Assessment/Figures and Maps` |
| Background / environmental studies (bio, cultural, geotech, arborist, traffic, etc.) | `01 Assessment` |
| Plans, site plans, engineering drawings, surveys | `02 Design` |
| Project descriptions, planning narratives, entitlement materials | `03 Planning` |
| Prior approvals, agency letters/memos, permit correspondence | `04 Permits/[Agency Name]` |
| Construction-phase compliance documents | `05 Compliance` |
| Deeds, title reports, easements, CC&Rs, vesting documents | `Legal` |
| Anything with no clear home | `Client Intake` (triage later) |

## Rules

- Route by document **type and function**, not by who sent it.
- A document that legitimately spans phases goes to the earliest phase it serves; note the cross-reference.
- Do not force-fit. If type is genuinely ambiguous, place in `Client Intake` and ask the user before final filing.
- Date intake files `YYYY-MM-DD_[Lead]_[Description]` for sortability.
