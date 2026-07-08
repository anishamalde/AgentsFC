# VAR — the match log

Every sub-agent appends one entry per turn. The Manager reads this to know what happened and decide what happens next.

This is your **observability layer**. When something goes wrong, this is the first file anyone reads.

Think of it as VAR — every incident on the pitch is recorded here, reviewable after the fact, and used to overturn or confirm decisions.

## Entry format

Each entry is ONE line. Prefix with the role. Use ISO 8601 timestamps.

```
[2026-07-08T14:22Z] [Manager] task=<one-line> kick-off
[2026-07-08T14:23Z] [Captain] facts=5 gaps=1 game-plan=written
[2026-07-08T14:25Z] [Striker] deliverable=<one-line> citations=5 blocked=no
[2026-07-08T14:27Z] [Defender:correctness] verdict=pass findings=0
[2026-07-08T14:27Z] [Defender:safety] verdict=flag findings=1
[2026-07-08T14:28Z] [Goalkeeper] decision=block reason=safety-defender-flagged
[2026-07-08T14:28Z] [Manager] play=1 outcome=block sub=defender:style->defender:tone
[2026-07-08T14:29Z] [Manager] shout-heard=cut-intro-paragraph
[2026-07-08T14:30Z] [Physio] injured=striker diagnosis=missing-context outcome=recovered
[2026-07-08T14:45Z] [Manager] full-time result=shipped
```

## Rules

- Every sub-agent writes their entry AFTER their turn.
- One line per entry. Keep it terse. If a decision needs more explanation, put it in the sub-agent's output — not the log.
- Never edit another sub-agent's entry. Append only.
- Do not delete entries. Even failed plays stay in the log — that's what makes VAR useful.

---

## Match log

<!-- Entries will be appended below during the match. -->
