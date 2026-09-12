# CONTRIBUTING.md

Engineering rules. This matters even solo, because it's what keeps multiple
agents (or the same agent across sessions) from stepping on each other.

## Branches

`type/short-description`, e.g. `feat/risk-engine-thresholds`,
`fix/attestation-nonce`, `sec/drift-module-bounds`.

One branch per task. Don't stack unrelated work on one branch.

## Commits

Small, one logical change each. Message format:

```
type: short summary

Longer explanation if needed — what changed and why, not just what.
```

Types: `feat`, `fix`, `sec`, `test`, `refactor`, `docs`, `chore`.

## Pull requests / merge checklist

Before merging any agent-produced change:

- [ ] `scripts/verify` passes (lint, typecheck, test, build)
- [ ] Self-review pass completed (see `TESTING.md`)
- [ ] Any touched `DECISIONS.md` item has a new dated entry, not a silent
      edit
- [ ] No files from the `SECURITY.md` off-limits list were touched
- [ ] Diff size matches the task — no unrelated files swept in

## Formatting / linting

Run `scripts/lint` before committing. Don't hand-format around a linter
you disagree with — fix the config or raise it, don't route around it.

## Review expectations

Every non-trivial change gets a review pass before it's considered done —
run as a separate step, not folded into implementation. See the review
prompt in `TESTING.md`. The reviewer looks for bugs, missing edge cases,
security issues, and architectural violations, and returns findings
ranked by severity without modifying anything.
