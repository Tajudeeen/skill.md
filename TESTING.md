# TESTING.md

What "done" actually means. An agent should never report a feature as
complete without having verified every line below.

## Definition of done

A feature is complete only when:

1. Implementation exists
2. Unit tests pass
3. Integration tests pass
4. Lint passes
5. Typecheck passes
6. Build succeeds
7. Self-review pass completed (below) with no unresolved high-severity
   findings

Run `scripts/verify` to check 2–6 in one shot. Don't claim "implemented
successfully" without having run it and seen it pass.

## Feature-specific definition of done (fill in per feature)

Example shape, for a gate feature:

- [ ] Risk engine correctly blocks the known bad-input cases
- [ ] Risk engine correctly allows the known good cases
- [ ] LLM classification output is logged but cannot bypass the risk
      engine's verdict
- [ ] Registry unavailability fails closed
- [ ] Attestation is written for both approve and block verdicts
- [ ] Escape hatch/timeout path tested independently as its own attack
      surface
- [ ] Tests cover both success and failure/rejection paths, not just the
      happy path

## Self-review protocol

After implementation, before calling anything done, run a separate review
pass — don't fold this into the same step as writing the code. Prompt
shape:

```
Review this implementation as a senior engineer.

Look for:
- bugs
- security issues (see SECURITY.md)
- missing edge cases
- duplicated logic
- architectural violations (see ARCHITECTURE.md)
- unnecessary complexity
- missing tests

Do not modify anything. Return findings ranked by severity.
```

Feed the findings back into implementation, fix, then re-run
`scripts/verify`.

## Verification checklist (before declaring anything production-ready)

**Engineering**: compiles, runs, types correct, tests passing, error cases
handled.

**Chain-specific**: correct chain/network used, addresses correct,
contract state correct, transactions verified, confirmations/finality
handled — don't assume submission means finality.

**Security**: who can call this, who can change this, can funds be
stolen, can state be manipulated, can transactions be replayed, can
external calls reenter, can the registry or an oracle be manipulated, can
an attacker force the escape hatch.

**Infrastructure**: what happens if the RPC fails, if the database fails,
if the indexer falls behind, after a chain reorg, if an external API goes
down.

**Production**: secrets protected, privileged accounts secured, emergency
recovery mechanism exists.

Don't call something production-ready until these have real answers, not
assumed ones.
