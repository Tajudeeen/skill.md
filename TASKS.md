# TASKS.md

Execution state. Structured by milestone, not an endless flat todo list, so
the agent has a sense of where the build currently sits.

## Deadline

[hackathon submission deadline]

## M0 — Spec locked

- [ ] Name confirmed available
- [ ] PROJECT.md filled in
- [ ] ARCHITECTURE.md diagram matches intended design
- [ ] Build directive / prompt for the coding agent written

## M1 — Foundation

- [ ] Repo scaffolded
- [ ] Contracts skeleton
- [ ] Deterministic risk engine skeleton
- [ ] Registry lookup wired (read-only)

## M2 — Core gate logic

- [ ] Risk engine rules implemented
- [ ] LLM classification layer wired, output feeds the gate but doesn't
      bypass it
- [ ] Attestation write path implemented
- [ ] Escape hatch / timeout path implemented and reviewed as its own
      attack surface

## M3 — Integration

- [ ] End-to-end flow: request → gate → verdict → attestation → execution
- [ ] Frontend or client wired, if applicable

## M4 — Hardening

- [ ] Self-review pass against `TESTING.md` checklist
- [ ] Security review pass against `SECURITY.md`
- [ ] `scripts/verify` green

## M5 — Submission

- [ ] Demo script / recording
- [ ] README polished
- [ ] Submitted

## Blocked

- [item] — blocked on [reason]

## Known bugs / technical debt

- [item]

## Completed (most recent first)

- [x] [item] — [date]
