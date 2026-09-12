# AGENTS.md

This file is the operating manual for any AI coding agent working in this
repository: Claude, GPT, DeepSeek, a local model, or a subagent spawned by one
of those. Read this file before touching any code.

## 0. Read order

Before making any change, read in this order:

1. `AGENTS.md` (this file) — how to behave
2. `PROJECT.md` — what we're building and for whom
3. `ARCHITECTURE.md` — how the system fits together
4. `DECISIONS.md` — why it's built this way, so you don't "fix" a deliberate choice
5. `TASKS.md` — what's currently in flight
6. `SECURITY.md` — what you must never touch or expose
7. `TESTING.md` — what "done" actually means

If a request conflicts with something in `DECISIONS.md` or `SECURITY.md`,
stop and flag the conflict instead of resolving it silently.

## 1. What this project is

A solo-built Web3 project (hackathon or production), reusing a common
architecture across repos: a deterministic risk engine gates every
sensitive action, an LLM layer classifies and explains but never has
unilateral authority, and every gate decision is posted on-chain as an
attestation. See `ARCHITECTURE.md` for the diagram specific to this repo.

## 2. Standing rules

- Never let an LLM output move funds, sign a transaction, or change
  privileged state directly. The model proposes, a deterministic layer
  validates, only then does a signer act. See `SECURITY.md`.
- Never invent contract addresses, transaction hashes, deployment results,
  or test outcomes. If you haven't run it, say so.
- Never edit `.env`, private keys, or any file listed as off-limits in
  `SECURITY.md`.
- Never modify a decision recorded in `DECISIONS.md` without calling it out
  explicitly and explaining what changed.
- Prefer the smallest correct change. Do not refactor unrelated code while
  fixing something else.
- If a README or comment disagrees with the actual code, trust the code and
  flag the mismatch.

## 3. Commands to run

```bash
scripts/lint        # static checks
scripts/typecheck    # type checks
scripts/test         # unit + integration tests
scripts/verify       # full gate: lint + typecheck + test + build
```

Run `scripts/verify` before reporting any task as complete. Do not report
"implemented successfully" without having actually run it.

## 4. Workflow stages

Don't jump straight to "build this." Work in stages:

```
SPEC → PLAN → IMPLEMENT → TEST → REVIEW → FIX → VERIFY
```

1. Restate the request as a spec: inputs, outputs, edge cases, definition
   of done (see `TESTING.md`).
2. Produce a short implementation plan and name the risks.
3. Wait for approval on anything touching `SECURITY.md`-listed areas,
   contract logic, or fund flows.
4. Implement the smallest correct version.
5. Run the deterministic checks in section 3.
6. Review your own diff as a skeptical senior engineer before calling it
   done (see `TESTING.md` review checklist).
7. Fix findings, re-run `scripts/verify`.
8. Summarize what changed and why, referencing any `DECISIONS.md` entries
   this touches.

## 5. Deterministic vs. model judgment

Don't ask the model to decide things a script can check exactly:

| Ask a script | Don't ask the model |
|---|---|
| Does it lint? | "Does this look clean?" |
| Do types check? | "Is the typing probably fine?" |
| Do tests pass? | "Did this probably work?" |
| Does it build? | "Is this ready?" |

The model's job is judgment calls that can't be reduced to a check: risk
classification, architectural tradeoffs, explaining *why* something failed.

## 6. Permission tiers

- **Read** (always allowed): inspect files, search code, read git history,
  read docs.
- **Write** (allowed without asking): create/modify source files, run local
  commands, run tests.
- **Restricted** (ask first): delete large amounts of code, change
  dependencies, touch migrations, modify CI, touch anything in
  `SECURITY.md`.
- **Forbidden** (never, even if asked): production deploys, private key or
  secret access, destructive production commands. See `SECURITY.md`.

## 7. Git discipline

One task, one branch, small commits, tests before merge. Don't touch more
files than the task requires — if a fix balloons into 40 changed files,
stop and explain why before continuing.
