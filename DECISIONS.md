# DECISIONS.md

Architectural decisions the agent must not silently "improve." If a change
touches something recorded here, it needs a new entry explaining what
changed and why — not a quiet override.

Format:

```
## YYYY-MM-DD

### Decision
[what was decided]

### Reason
[why]

### Rejected
[alternatives considered and why they lost]

### Consequence
[what this commits us to]
```

## Worked examples (from prior projects — replace with this project's own)

## 2026-08-XX

### Decision
Include a `timeoutRelease` escape hatch on the transfer/redemption gate.

### Reason
If the risk engine or LLM classification layer becomes unavailable, funds
must not be permanently stuck behind a gate that can no longer produce a
verdict.

### Rejected
No escape hatch — rejected because a stuck gate is a worse failure mode
than a delayed release.

### Consequence
Adds a timeout path that must itself be reviewed as a security surface —
an attacker shouldn't be able to force the timeout deliberately to bypass
the gate.

## 2026-08-XX

### Decision
Project name confirmed clean before build start; alternates eliminated
early (taken / conflicting).

### Reason
Cheaper to resolve naming collisions before code and docs are written
around a name than after.

### Rejected
Proceeding with a name pending trademark/availability check.

### Consequence
None — naming locked before implementation.

---

Replace the worked examples above with this project's actual log as
decisions get made. Keep entries even after a decision is later reversed —
record the reversal as a new dated entry, don't delete the original.
