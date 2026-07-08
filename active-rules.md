# Active Rules — the touchline

The user (or you as the Manager) can push instructions into the match without stopping it. The Manager checks this file at the start of every play.

Think of it as the manager shouting instructions from the touchline mid-match.

## Format

```
[2026-07-08T14:35Z] [MANAGER'S SHOUT] Stop over-explaining. Cut the intro paragraph.
[2026-07-08T14:36Z] [HALF-TIME NOTE]  Defender:style is being too picky — relax the tone check.
[2026-07-08T14:40Z] [RED CARD]        End the match. Something is wrong.
```

## Priorities

- **RED CARD** — end the match immediately. Nothing ships. Sub-agents stop. Match is void.
- **MANAGER'S SHOUT** — apply on the next sub-agent turn. Urgent touchline instruction.
- **HALF-TIME NOTE** — apply when it naturally fits. Not urgent.

## Rules

- The user writes here at any point during the match.
- The Manager checks this file at the start of every play (Captain turn onwards).
- The Manager logs to `var.md` when they've absorbed a shout (`[Manager] shout-heard=<one-line>`).
- Do NOT delete instructions once applied. Keep the history — that's the audit trail.
- A RED CARD is final. Once written, the match ends. Start a new match with a new brief if you want to try again.

---

## Active instructions

<!-- User appends new instructions below during the match. -->
