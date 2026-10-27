# ⚽ Agent FC: a file-based agent squad

Orchestrate a squad of coding-agent sub-agents using nothing but markdown files. No harness, no runtime, no infrastructure. Point Claude Code, Cursor, Cline or similar at this folder and the agent adopts each role, coordinates through shared files, and leaves an audit trail you can read afterwards.

This is the "before" picture for the Agent FC talk: what multi-agent orchestration looks like when all you have is prompts and files. It works well for personal-sized tasks. When a task needs real gates, parallel verification and audits, graduate to a harness.

> The model is non-deterministic. Files can't fix that. A harness can.

## Why this exists

- **Roles are files.** Each sub-agent has a single role file with its scope, rules and output shape.
- **State is files.** Shared artifacts carry the match from kick-off to ship, so every decision is inspectable.
- **The Manager coordinates, it doesn't do the work.** Only the Manager spawns sub-agents. Sub-agents never spawn each other.

## The squad

**On the pitch: sub-agents that run the play**

| File | Role | Lifecycle stage |
|---|---|---|
| `manager.md` | The Manager. The coordinator, off the pitch. Paste this in first. | Learn (owns the retro) |
| `agents/captain.md` | Reads the task, retrieves grounded facts, writes the Game Plan. | Plan |
| `agents/striker.md` | Produces the deliverable. The only creative sub-agent. | Build |
| `agents/defender.md` | Adversarial critic, one lens per instance. Spawn several in parallel when needed. | Test |
| `agents/goalkeeper.md` | Ship-or-block gate. Always runs. | Ship |

**On the bench: called on failure**

| File | Role | When called |
|---|---|---|
| `agents/physio.md` | Diagnoses and unblocks stuck sub-agents. | Any stage, on failure |

## The match artifacts

Five shared files carry state through the match.

| File | Purpose | Written by | Read by |
|---|---|---|---|
| `match-brief.md` | Task and constraints, written before kick-off | User (or Manager on their behalf) | Everyone, once |
| `game-plan.md` | The Captain's tactical plan | Captain, once per match | Striker, Defenders, Goalkeeper, Manager |
| `var.md` | VAR log: one line per sub-agent turn | Everyone (append only) | Everyone |
| `manager-notebook.md` | Manager's private reasoning | Manager only | Manager only |
| `active-rules.md` | Mid-match instructions from the user | User | Manager, at the start of every play |

## How a match runs

```
                 Kick-off
                    │
match-brief.md ─┐   ▼
active-rules.md ─┼─► Manager ─► Captain writes game-plan.md
                 │      │              │
                 │      │              ▼
                 │      │           Striker builds
                 │      │              │
                 │      │              ▼
                 │      │      Defender(s) critique
                 │      │              │
                 │      │              ▼
                 │      │   Goalkeeper: ship or block
                 │      ▼              │
                 │  manager-notebook ◄─┤ var.md (VAR log)
                 ▼                     ▼
              ship, sub, or red card
              
Physio: on the bench, sent on when a sub-agent goes down.
```

### Touchline shouts (`active-rules.md`)

You can steer a running match without stopping it. Append an entry with one of these priorities:

- **RED CARD**: end the match immediately. Nothing ships. The result is void.
- **MANAGER'S SHOUT**: apply on the next sub-agent turn.
- **HALF-TIME NOTE**: apply when it fits. Not urgent.

Entries are never deleted. They stay as the audit trail.

## Running it

1. Fill in `match-brief.md` with your task, success criteria and constraints.
2. Open your coding agent in this directory.
3. Tell it:

   > Read `manager.md` and act as the Manager. The task is in `match-brief.md`.

4. Watch the play unfold in `var.md`.

The Manager returns a short trace (who did what, in order, and whether the Goalkeeper shipped) and points you at `var.md` for the full detail.

## Repo layout

```
.
├── README.md              # you are here
├── manager.md             # coordinator role (start here)
├── match-brief.md         # your task goes here
├── game-plan.md           # written by the Captain
├── var.md                 # VAR log: the audit trail
├── active-rules.md        # mid-match instructions
├── manager-notebook.md    # Manager's private notes
└── agents/
    ├── captain.md
    ├── striker.md
    ├── defender.md
    ├── goalkeeper.md
    └── physio.md
```

## Limits

- **Good for:** one-off personal tasks, prototyping a multi-agent workflow, learning how the roles interact.
- **Not good for:** anything that needs deterministic gates, parallel verification, a durable audit store or multiple stakeholders.
- **Ship policy is advisory.** The Goalkeeper is an LLM following a markdown rulebook, so it can be talked out of a block. A harness turns it into a real state-machine step.

## Where this ends and the harness begins

| | This scaffold | A harness |
|---|---|---|
| Coordination | One conversation, Manager holds state | State machine with explicit transitions |
| Audit trail | Markdown file | Queryable trace store |
| Ship gate | Hopeful Goalkeeper | Deterministic check |
| Human in the loop | You, editing files | Approval step via task tokens |

## Credits and references

- [agentsparty.dev](https://agentsparty.dev): the D&D-flavoured version of this pattern, where the file structure started.
- [Kourtney Meiss's Pokémon orchestration system](https://dev.to/knmeiss/training-your-pokemon-my-ai-orchestration-system-k7p): the shared journal and circuit-breaker pattern.
