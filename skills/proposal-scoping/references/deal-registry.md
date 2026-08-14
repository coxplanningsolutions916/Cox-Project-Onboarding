# The Deal Registry — the Sales Team's live record of a deal

The Claude Sales Team project keeps a **deal registry** the same way the client project
registry works after onboarding: one durable, human-readable record that is **read first**
at the start of any work on a deal and **appended to** at every milestone. It spans three
places, tied together by two keys.

## The two keys (put them at the top of every registry note)

| Key | Where it lives | How to find it |
|---|---|---|
| **Automator opp id** (`ghl:<oppid>`) | the Automator opportunity | `list_opportunities(query="<name>")` |
| **Productive deal id** | the Productive sales deal (pipeline `91540`) | from the `[ghl:<oppid>]` tag written into the deal note by the sync; recorded here so you don't have to re-derive it |

Everything else about the deal can be reconstructed from these two.

## Who drives what (this is the important part)

Cowork can write some of the deal card directly and some only indirectly — know which:

- **Automator opportunity = the live pipeline card.** Cowork drives it directly via the
  **Automator MCP**: `update_opportunity` (name, `monetary_value`, `stage`, `status`,
  `contact_id`) and `create_contact`. This is where you set the client contact, the value,
  and advance the stage.
- **Productive deal card mirrors it via the daily sync.** `sync_sales.py` propagates the
  Automator **stage + value** onto the Productive deal (value in cents). So you do **not**
  set Productive `deal_value` or stage by hand from Cowork — set them on the Automator opp and
  let the sync carry them over. (The Productive MCP has **no** deal-find / deal-update tool.)
- **Productive holds the estimate.** The scoped deliverables become **services** on the
  Productive deal via `add_service(deal_id, …)` — that IS the estimate (see
  `proposal-generation.md`). Set the Automator `monetary_value` = the services total so the
  two systems agree and the sync doesn't fight you.
- **The GD proposal folder holds the durable registry note** (below) — the one artifact a
  human reads to know the whole story.

## The registry note — `00_Deal_Registry.md`

Lives at the top of the lead's proposal folder (`Sales/Proposals/[Lead Name]/`, scaffolded by
the intake skill). Created at intake, appended at every step, via the Google Drive MCP
(`create_file` / `read_file_content`). Format:

```
# Deal Registry — <Client legal entity> (<short project / property>)

- Automator opp:   ghl:<oppid>
- Productive deal: <deal id>
- Client entity:   <legal entity>          Contact: <name / email / phone>
- Agent / referrer: <name>                 (if any — e.g. the listing agent)
- Property / APN:  <address> / <APN>
- DDL step:        <1a | 1b | 2 | …>
- Current stage:   <pipeline stage>         Value: $<amount>

## Log
- YYYY-MM-DD  intake screened — <one line: what the go/no-go looked like>
- YYYY-MM-DD  scope drafted — <n> deliverables; see 02_Scope
- YYYY-MM-DD  budget set — fee $<amount> (fee backup: <file>), margin <n>%
- YYYY-MM-DD  proposal generated — <file / artifact>
- YYYY-MM-DD  proposal sent (Automator) — stage → Proposal Sent
- YYYY-MM-DD  signed / won — onboarding handed off
```

Keep log lines to one sentence. It is a ledger, not a narrative — the scope, fee backup, and
proposal live in their own numbered subfolders; the registry just points to them and records
what happened when.

## The loop (do this on every deal, both skills)

1. **Read first.** Before touching a deal, read its `00_Deal_Registry.md` (and, if useful,
   `list_opportunities`/`get_opportunity` + `list_services`) so you restore state instead of
   re-deriving or duplicating it.
2. **Act.** Do the intake step or the scope/budget step.
3. **Write the card.** Update the Automator opp (`update_opportunity` — value, stage, contact).
   Write/refresh services on the Productive deal if the estimate changed.
4. **Append the log.** Add a dated one-line entry to `00_Deal_Registry.md`.

Nothing is "done" until step 4 — the registry is the source of truth for where the deal is.
