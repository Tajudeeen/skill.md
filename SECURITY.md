# SECURITY.md

What the agent must never touch, expose, or assume. This matters more than
usual here: these projects exist specifically to gate fund-moving actions,
so the agent working on the gate has to hold itself to the same standard
the gate enforces on everything else.

## Off-limits files (never read into context, never edit, never log)

- `.env`, `.env.local`, `.env.production`
- Any private key file or keystore
- Production database credentials
- Any file listed in `.gitignore` under a `secrets/` or `keys/` path

Use `.env.example` to document required variables without real values.

## The core rule for AI + funds

Never let a model's output move funds, sign a transaction, or change
privileged state directly. The required shape is:

```
AI proposes
    ↓
Deterministic risk / policy engine validates
    ↓
Transaction validation
    ↓
Signer
    ↓
Blockchain
```

Not:

```
AI
 ↓
Private key
 ↓
Blockchain
```

If a change would let a model output reach a signer without passing
through the deterministic gate, stop and flag it — don't implement it even
if the request seems to ask for it directly.

## Trust assumptions to keep explicit

- **Registry data** (e.g. ERC-8004 reputation lookups): treat as
  potentially stale or wrong. Define what happens on registry
  unavailability — fail closed, not open.
- **LLM classification output**: advisory input to the gate, never a
  verdict on its own. If the model's output alone can approve a
  transaction, that's a design bug, not a feature.
- **Session keys**: scope them to the minimum permission set the flow
  actually needs. Never grant a session key broader authority than the
  gate itself is willing to approve.
- **Escape hatches / timeouts**: any bypass path (like a timeout release)
  is itself a security surface — review it as carefully as the main gate,
  since it's the path an attacker would try to force.

## Permission tiers (mirrors AGENTS.md)

- **Forbidden, always**: production deploys, private key or secret
  extraction, destructive production commands, disabling the gate to "just
  test something."
- **Restricted, ask first**: dependency changes, migrations, CI changes,
  anything touching signer logic or key scoping.
- **Write, no need to ask**: application code, tests, docs, local dev
  commands.

## Unsafe commands

Never run, suggest, or silently work around:

```bash
npm install --force
npm install --legacy-peer-deps
```

without first understanding and stating the actual dependency conflict.

## Reporting

If you find something that looks like a real vulnerability while working
on unrelated code, stop and report it before continuing — don't fix it
silently as a side effect of an unrelated change, and don't leave it
unmentioned.
