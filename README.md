# Kubuntu Dual-Boot Install Fest

Installing Kubuntu 26.04.1 alongside Windows on about 210 student laptops, run in waves. One stock ISO for everyone. Status: draft, dry run pending.

## Start here

| If you are... | Read |
|---|---|
| A participant | `BRIEF.md` (before the event) |
| An operator (owns participants) | `MASTER.md`, direct step by step |
| In a department (specialist team) | `RUNBOOK.md` |
| A Super | `MASTER-FLOWCHART.md`, then `RUNBOOK.md` and `ROADBLOCKS.md` as needed |

## Files

- `BRIEF.md`: plain-language preflight checklist for participants.
- `MASTER.md`: the only place the steps live. Everything else points back to it.
- `MASTER-FLOWCHART.md`: a picture of MASTER, with side exits to departments.
- `RUNBOOK.md`: one file, non-standard cases only, grouped by department.
- `ROADBLOCKS.md`: every known problem, its owner department, and a status tag.
- `PROBLEM-SLIP.md`: the paper slip each participant fills in; slips are collected at handoff.
- `STATS.md`: install times and rescue rates. Filled in after the dry run.

## Roles

- **Participant:** does the simple steps, asks for help when stuck.
- **Operator:** reads the MASTER checklist, takes over operator-only steps, calls a Super if anything looks even slightly wrong.
- **Department:** fixes a laptop that hit a roadblock and sends it back to the rejoin point in MASTER.
- **Super:** escalation for everything else.

## USB color code (operators only)

- Blue: Kubuntu ISO
- Red: missing drivers and firmware
- Green: Windows recovery (worst case)

## Tags

- `[OPEN]` undecided. `[CONFIRM]` assumed, needs a yes. `[UNTESTED]` not yet run on real hardware.
- Edit by hand. Remove a tag when the item is settled.
