<!--
  PRD.md — web3-senior-engineer-auditor skill
  Aesthetic: Ambit — dark field, lime accents, Georgia serif
-->
# PRD — web3-senior-engineer-auditor

## 1. The artifact this skill produces

When this skill runs, it outputs:

1.  A **risk register** — a markdown table of every finding, rated 1–5.
2.  A **trust boundary diagram** — text-art, no images.
3.  A **code-level audit checklist** — per-contract findings.
4.  A **deployment-readiness signal** — pass, warn, fail.

This is not a report template. This is the **engineering contract** between
the auditor and the team. Every entry must be falsifiable.

## 2. The four review modes

The engineer selects one mode per invocation. No defaults.

### Mode A — Security Audit (default)

Full pass: access control, reentrancy, oracle trust, signature validity,
upgrade paths, economic attack vectors, MEV exposure.

> Requires: full repo access, contract source + compiler version, deployment
> script, existing test suite.

### Mode B — Architecture Review

Reviews system design only. No line-by-line code scan.

> Requires: architecture diagram (hand-drawn scan), trust boundary map,
> component data flow.

### Mode C — Deployment Gate

Reviews operational readiness only.

> Requires: deployment scripts, signer addresses, CI/CD config, monitoring
> config, incident response playbook.

### Mode D — Agent Behavior Audit

Reviews LLM-agent decision paths against the deterministic gate.

> Requires: agent source code, prompt templates, on-chain attestation flow,
> rollback conditions.

## 3. The five output ratings

Every finding gets exactly one of these ratings:

| Rating     | Meaning                                                                 |
|------------|-------------------------------------------------------------------------|
| **CRIT**   | Direct loss of funds or protocol state. Must fix before any deploy.     |
| **HIGH**   | Exploit path exists but requires specific conditions. Fix in 48h.       |
| **MED**    | Architectural weakness. Not exploitable now but could be. Fix in sprint.|
| **LOW**    | Style / hygiene / gas. Fix in backlog.                                  |
| **INFO**   | Observation — not a bug, but worth documenting.                         |

> ⚠️ **No "MEDIUM-HIGH" or "P1-P4" variants.** One label. One action. This
> skill does not believe in rating paralysis.

## 4. The trust boundary map (template)

Every audit begins with this template, filled in by hand:

```text
[ Frontend (web UI) ]
        |
        v
[ Wallet / Signer ]  — user-controlled
        |
        v
[ Backend / Agent ]  — team-controlled? semi-trusted?
        |
        +----> [ On-chain Registry ]  — immutable
        +----> [ On-chain Contracts ] — upgradeable? by whom?
        |
        v
[ RPC Provider ]      — trusted for delivery, NOT for truth
        |
        v
[ Blockchain State ]  — source of truth
```

> Every arrow is a trust boundary. Every boundary must have a threat model.
> If you can't name the threat, you can't close the boundary.

## 5. The finding format

Every finding must follow this exact shape:

```markdown
> ⚠️ FINDING-07 · CRIT

> Contract: `src/MyContract.sol:120`
> Title:     Reentrancy on withdraw()
> Path:      `withdraw()` → external call to ERC20 → callback into
>            `balanceOf()` → modifies `_balances` mid-flow

> Impact:    Attacker drains all liquidity in a single tx.
>            Estimated loss: 100% of pool value (~$2.3M).

> Fix:       Apply `nonReentrant` modifier. Use checks-effects-
>            interactions pattern. Validate token contract before call.

> Status:    Open
```

No free-text summaries longer than 4 lines. Every finding must have:
- A unique ID
- A severity rating
- A file:line pointer
- A one-sentence title
- A concise impact statement
- A concrete fix

## 6. The deployment-readiness signal

After the full audit, the skill delivers exactly one of three signals:

> ✓ **PASS** — All CRIT and HIGH findings resolved. No open MED or above.

> ⚠️ **WARN** — All CRIT resolved. One or more HIGH or MED open, with
> documented mitigation plan and timeline.

> ✗ **FAIL** — Any CRIT remains open. Do not deploy.

This signal is the **only** thing a non-technical stakeholder needs to read.
Everything else is supporting evidence.

## 7. Non-goals

This skill does **not** produce:

- Executive summaries longer than 4 paragraphs
- Slide decks
- PDF reports
- Risk matrices with color-coded heat maps
- "Business impact" commentary beyond fund loss estimation

If you ask for any of the above, this skill will ask you to restate the
request using the four review modes and the five output ratings defined here.
