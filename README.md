# ⚽ Agent FC — File-Based Squad

A simple file-based agent orchestration setup for the **Agent FC** talk. No harness, no runtime, no deployed infrastructure — just markdown files that a coding agent (Claude Code, Cursor, Cline, etc.) reads to adopt roles, coordinate, and produce an auditable trail.

This is the **before** in the talk's arc: how you'd orchestrate a squad of agents purely through prompts and files, when you don't have a harness. It works well for personal-shaped tasks. When the task becomes a real pipeline with gates and audits, you graduate to a harness.

> The model is non-deterministic. Files can't fix that. A harness can.

## The squad

The Manager is a coordinator. Every teammate is a **sub-agent** — a specialist spawned with its own role file, its own scope, and its own trace entry. Sub-agents don't spawn each other; only the Manager spawns them.

**On the pitch — sub-agents that run the play:**

| File | Sub-agent | Lifecycle stage |
|---|---|---|
| `manager.md` | The Manager (not a sub-agent — the coordinator). Sits off the pitch. This is what you paste in first. | Learn (owns the retro) |
| `agents/captain.md` | On-pitch #10. Reads the game, retrieves grounded facts, writes the Game Plan. | Plan |
| `agents/striker.md` | Produces the deliverable. The one creative sub-agent. | Build |
| `agents/defender.md` | Adversarial critic. One lens at a time — spawn multiple Defender sub-agents in parallel with different lenses when the task calls for it. | Test |
| `agents/goalkeeper.md` | Output-boundary gate. Ship or block. Always runs. | Ship |

**On the bench — sub-agent called on failure:**

| File | Sub-agent | When called |
|---|---|---|
| `agents/physio.md` | Diagnoses and unblocks stuck sub-agents. Only enters when someone's down. | On failure (any stage) |

## The match artifacts

Five shared files carry state through the match. Every sub-agent knows which ones to read and write.

| File | Purpose | Who writes | Who reads |
|---|---|---|---|
| `match-brief.md` | The team-talk before kick-off — task + constraints | User (or Manager on their behalf) before kick-off | Everyone, once |
| `game-plan.md` | The Captain's tactical plan for this match | Captain, once per match | Striker, Defender, Goalkeeper, Manager |
| `var.md` | The **VAR log** — one line per sub-agent turn | Everyone appends their turn | Everyone reads before their turn |
| `manager-notebook.md` | Manager's touchline notepad — private reasoning | Manager only | Manager only |
| `active-rules.md` | Touchline shouts — mid-match instructions from the user | User (mid-match) | Manager (checks every play) |

That's it. Five files. Everything else — sub decisions, plays, retries — happens by sub-agents reading these files, appending to `var.md`, and returning control to the Manager.

## How to run it

1. Fill in `match-brief.md` with the task.
2. Open your coding agent (Claude Code, Cursor, etc.) in this directory.
3. Point it at `manager.md`:
   > "Read `manager.md` and act as the Manager. Task is in `match-brief.md`."
4. The Manager reads the sub-agent files as needed and coordinates them through the play.

The Manager writes the trace as it goes. You can watch the play unfold in `var.md`.

## The match, in one glance

```
                        Kick-off
                            │
match-brief.md  ─┐          ▼
active-rules.md ─┼─►  Manager  ─►  Captain writes game-plan.md
                 │       │              ↓
                 │       │         Striker builds
                 │       │              ↓
                 │       │        Defender(s) critique
                 │       │              ↓
                 │       │       Goalkeeper: ship or block
                 │       ↓              ↓
                 │  manager-notebook ← var.md (VAR log)
                 │       ↓
                 ▼   ship, sub, or red-card

Physio: on the bench, waved on when a sub-agent goes down injured.
```

**Match-ending priorities in `active-rules.md`:**
- **RED CARD** — match ends, result void
- **MANAGER'S SHOUT** — urgent instruction, apply next turn
- **HALF-TIME NOTE** — instruction to apply when it fits

## Where this ends and the harness begins

- **This scaffold** — good for one-off personal tasks. The Manager holds the coordination in one conversation. VAR log is a plain markdown file. Ship policy is enforced by a hopeful Goalkeeper.
- **A harness** — what you graduate to when you need parallel verification, deterministic gates, audit trails, or multiple stakeholders. The Goalkeeper becomes a real ship-or-block step in a state machine. VAR becomes a queryable trace store. The Manager (you, the human) intervenes via `waitForTaskToken`. See the talk outline for the escalation path.

## References

- [agentsparty.dev](https://agentsparty.dev) — the D&D-flavoured version of this pattern; where the file structure came from
- [Kourtney Meiss's Pokemon system](https://dev.to/knmeiss/training-your-pokemon-my-ai-orchestration-system-k7p) — the shared journal + circuit-breaker pattern
- Talk outline: `~/anishapm/AnishaAmazonObsidian/talks/agent-fc-talk-abstract.md`
- `visualiser.md` — how we could put a website on top of this
