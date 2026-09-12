<!--
  SETUP.md — web3-senior-engineer-auditor skill
  Aesthetic: Ambit — dark field, lime accents, Georgia serif
-->
# Setup Guide — web3-senior-engineer-auditor

This skill ships with **zero assumptions**. It does not assume you have a
particular chain, tooling, or even a local environment. It assumes you have
`git`, a shell, and a wallet.

## 0. The three questions you answer first

Before touching code, the engineer must answer:

1.  **Which chain?** EVM, SVM, Aptos, Flare, BNB Smart Chain, Solana, etc.
2.  **What is the threat model?** Hot wallet, cold wallet, multisig, HSM?
3.  **What is the blast radius?** Funds on-chain? User identity? Protocol state?

Everything in this setup guide branches from those three answers.

## 1. Environment bootstrap

Minimal tooling set, pinned versions:

```bash

# Core toolchain — do not deviate
git        >= 2.40
node       >= 20.x   (use fnm)
foundry    latest   (curl -L getfoundry.sh | bash)
hardhat    latest   (npx hardhat)
python     >= 3.11  (use uv)
jq         >= 1.7
```

No global npm installs beyond the above. No "convenience" scripts. This is a
security review — the attack surface must be explicit.

## 2. Clone posture

Do **not** clone into your home directory. Use a dedicated workspace:

```bash
mkdir -p ~/sec/workspace/<protocol-name>
cd ~/sec/workspace/<protocol-name>
git clone <repo>
```

Every repo you touch for a given audit gets its own subdirectory. This prevents
cross-contamination of `node_modules`, env files, and cached state.

## 3. Wallet and signer setup

This is the most fragile part. Do it once, write it down:

> ⚠️ Never use a production wallet for testing. Generate a dedicated ephemeral
> wallet for each review cycle.

```bash
# Generate ephemeral dev wallet — NEVER use for real funds
cast wallet new

# Or from mnemonic (12 words only, no derivation path tricks)
cast wallet new --words 12
```

For each chain under review:

1.  Fund the ephemeral wallet with testnet tokens (faucet or `hardhat node`).
2.  Record the private key in a vault — never in `.env`, never in git.
3.  Rotate the key after the review is complete.

## 4. RPC isolation

Never use public RPCs in your review environment. Spin up:

```bash
# Local fork — safest for audits
anvil -f <CHAIN_RPC_URL> --block-time 1

# Or use a dedicated RPC provider with rate limits
# Alchemy, Infura, QuickNode — pick one, configure once
```

> ⚠️ Public RPCs leak your activity pattern. Even in testing, assume the RPC
> provider logs every call you make.

## 5. Dependency hygiene

Before installing any package:

```bash

# Check the dependency tree first
npm ls <package>     # or pnpm why <package>
# Is it already there? Is it pinned? Is it audited?

# Lockfile check
git diff --name-only HEAD~1 | grep -q "pnpm-lock.yaml"
# If the lockfile changed, re-audit.
```

This skill refuses to install unpinned dependencies. Period.

## 6. The review environment checklist

Before you begin any review, confirm:

- [ ] Isolated workspace directory exists
- [ ] Ephemeral wallet generated and funded
- [ ] RPC endpoint configured (not public default)
- [ ] Foundry or Hardhat installed and verified
- [ ] Node version matches project requirement
- [ ] No `.env` files committed to git
- [ ] `pnpm/audit` or `npm audit` run (0 critical, 0 high)

## 7. Logging posture

This is a review skill — not a deployment skill. Logs are artifacts:

```bash

# All commands that touch a chain get tee'd
cast call ... 2>&1 | tee ~/sec/logs/$(date +%s).call.log

# Every output is reviewable later
# Logs are NOT in the repo — they are in the reviewer's vault
```
