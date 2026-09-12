# PROJECT.md

Product-level context. This exists so the agent doesn't make a technically
sound decision that's wrong for what this project actually needs to win or
ship. Fill in the bracketed fields per repo — everything else is a real
default drawn from how these projects tend to be built.

## What we're building

[PROJECT NAME] — one paragraph on what it does and the core problem it
solves. Example shape: "a pre-transaction guardian that screens AI-agent
calldata against a reputation registry and a behavioral drift model before
release."

## Context

- Event / bounty: [hackathon name, track]
- Chain: [chain]
- Deadline: [date]
- Stage: [prototype / hackathon submission / hardening for production]

## Target users

Who actually uses this. For most of these projects it's one of:
- AI agents transacting autonomously (this project is infrastructure they
  call, not a human-facing app)
- Developers integrating the gating layer into their own agent or protocol
- End users whose funds are protected by the gate

## Core problem

What breaks without this project. Be specific about the failure mode being
prevented (e.g. an agent signing a malicious calldata blob, an unvetted
counterparty draining a session key) rather than a generic "security" claim.

## Goals

- [primary judged criterion or shipped feature]
- [secondary feature]

## Non-goals

Be explicit here — this is what stops the agent from gold-plating scope
mid-build. Example: "not building a full wallet," "not supporting non-EVM
chains in this pass," "not implementing governance."

## Technical stack

- Contracts: [Solidity version, framework]
- Chain / L2: [chain]
- Backend / agent runtime: [language, framework]
- Frontend: [if any]
- Key external deps: [ERC-8004 registry, oracle, session-key provider, etc.]

## Important constraints

- Solo build, [timeframe] window — favor shipping a defensible, judged demo
  over exhaustive coverage.
- Reused architecture: deterministic risk engine + LLM classification layer
  + on-chain attestation. Don't reinvent this per project; adapt it.
- [any chain-specific constraint: testnet-only, faucet limits, etc.]
