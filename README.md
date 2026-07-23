# Oakenscroll's Office

Where certainty gets graded. ΔΣ=42

A local-first calibration ledger. Write down what you believe and how hard —
*"the PR merges this week, 70%"* — resolve it when the world weighs in, and
the mirror shows whether your 70% means 70%.

The niche this fills (verified July 2026): PredictionBook is retired, Fatebook
is cloud-only, and the community fallback is spreadsheets. Nothing local-first
or terminal-native existed. Your record of being wrong is exactly the data
that should never live on someone else's server.

Design log: [`docs/design/the-almanac.md`](https://github.com/rudi193-cmd/safe-app-store/blob/master/docs/design/the-almanac.md)
in safe-app-store (named before the D1 rename to Oakenscroll's Office).

## Three rules

1. **Append-only.** A stated prediction is never edited or deleted. Change
   your mind (`c`) and both numbers stay on the record; only the latest is
   scored. VOID keeps its record too.
2. **State the claim in the direction you believe.** Confidence is P(true) in
   50–99%. The ledger refuses hedged-backwards entries loudly.
3. **Nothing leaves the device.** The core (`office_db.py`, `calibration.py`)
   is structurally incapable of egress — an AST test forbids network imports.
   The one outward seam (`willow_bridge.py`) is optional, off by default, and
   sits outside that zone.

## Run from source (standalone)

No Willow checkout, Postgres, or network required — the ledger lives in
`~/.willow/store/oakenscrolls-office/office.db` (override: `OAKENSCROLL_DB`).

    ./dev.sh        # macOS/Linux — the TUI
    ./dev.ps1       # Windows (PowerShell)
    python3 web.py  # the mirror: http://127.0.0.1:8437 (reliability diagram)

## Keys

| Key | Action |
|-----|--------|
| `n` | State a claim → confidence (`5`–`9` → 50–90%, or exact like `85`) → due (`+3d`/`+2w`/`+1m` or open-ended) |
| `t` / `f` | Grade the surfaced due item (or selected row) true / false |
| `o` | Void — unresolvable or ambiguous; the record is kept |
| `s` | Snooze the surfaced due item for this session |
| `c` | Change your mind — revise confidence, old number stays on the record |
| `Tab` | Cycle view: on the record / graded / scorecard |
| `q` | Quit |

At open, due predictions surface one at a time. One keypress each. No
journaling guilt.

## The scorecard

Overall Brier and log scores, then the reliability table: for each stated
band (50–60% … 90–99%), what you said vs. how often you were right. `web.py`
draws the full diagram — the dotted diagonal is perfect calibration; dots
below it are overconfidence.

## Willow seams (optional, off by default)

- `OAKENSCROLL_PROACTIVE=1` publishes due predictions to
  `$WILLOW_HOME/signals/oakenscroll_dew.json` (the dew pattern).
- `willow_bridge.promote_resolved(pid, ingest=...)` builds a knowledge atom
  from a graded prediction for an injected `knowledge_ingest` — this module
  never imports Willow and never phones home on its own.

## Tests

    python3 -m pytest tests/ -q

Covers the math, the ledger rules (append-only, lifecycle, scoring pairs),
the web routes (no socket needed), the bridge seams, and the no-egress AST
scan.
