# ARCHITECTURE.md

The system map. Fill in the per-project boxes; the shape below is the
reusable pattern this build carries across projects — don't redesign it
from scratch each time.

## Standard flow

```
User / calling agent
        |
        v
Frontend or agent client
        |
        v
Backend / API layer
        |
        +--> Deterministic risk engine  --+
        |    (rules, thresholds, limits)  |
        |                                 v
        +--> LLM classification layer --> Gate decision
        |    (context, intent, drift)     |
        |                                 v
        |                          Guardian / Decision Shield
        |                          (pre-transaction gate)
        v                                 |
   ERC-8004 registry lookup   <-----------+
   (reputation / trust score)             |
                                           v
                                  Approve / block verdict
                                           |
                                           v
                              On-chain attestation
                              (Merkle-root snapshot or
                               direct event log)
                                           |
                                           v
                                     Signer / session key
                                           |
                                           v
                                       Blockchain
```

## Components (fill in per project)

- **Frontend**: [what exists, if anything]
- **Backend / agent runtime**: [language, where it runs]
- **Deterministic risk engine**: [what it checks — amount limits, recipient
  allowlists, calldata decode, rate limits]
- **LLM classification layer**: [what it's asked to judge — intent,
  anomaly, reputation context — and explicitly NOT asked to decide, i.e.
  it never has final authority]
- **Registry / reputation source**: [ERC-8004 registry or equivalent —
  what it returns, how fresh the data is, what happens if it's
  unavailable]
- **Attestation**: [what gets written on-chain, how often, format]
- **Signer**: [session key / EOA / multisig — and what it is NOT allowed
  to do without a passed gate]
- **Database / off-chain state**: [if any]

## Trust boundaries

For each component above, be explicit:

- Who controls it?
- What happens if it returns wrong or stale data?
- What happens if it's unavailable?
- Could a malicious input reach the signer without passing the gate?

## Data flow

Trace one real transaction end to end here once implementation starts —
concrete example beats an abstract diagram. Update this section as the
design solidifies; don't let it drift from the code.

## Design decisions that shaped this

See `DECISIONS.md` for the reasoning behind specific choices (naming,
escape hatches, architecture reuse). Don't re-litigate those here — link
to them.
