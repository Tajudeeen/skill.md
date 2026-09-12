# Research: How winsznx wins hackathons — study of his build patterns

## Source
- GitHub: https://github.com/winsznx (Tim, "Dev, building things", 245 repos, 128 followers)
- Twitter: https://x.com/winsznx
- Blog: https://winszn.xyz
- LinkedIn: https://linkedin.com/in/winszn
- About-me repo (winsznx/winsznx): blockchain protocol + backend engineer. Claims "Nine hackathon placements." Mainnet on Solana, Ethereum, Starknet, Bitcoin Cash, Somnia, Tempo.

Profile self-description:
"Building production infrastructure for cross-chain payments, agent execution, and yield primitives."

---

## Top winning repos by evidence

### 1. bull-rush (352 stars, 2 forks) — the biggest winner evidence
- 3D neon endless runner game for memecoin The Black Bull ($ANSEM)
- Live on BOT Chain mainnet (chain 677) — 3 contracts deployed + source-verified on BOTScan
- 213 passing tests across 4 suites (97 unit, 35 sim, 38 integration, 43 contract)
- Play → https://trybullrush.xyz

### 2. routedock (7 stars, **45 forks**) — highest community adoption signal
- Unified payment execution layer for Stellar x402 + MPP
- TypeScript SDK, npm published, 45 forks = widely reused
- Live testnet transactions with real tx hashes on stellar.expert
- Migration docs: Vercel → Cloudflare Workers

### 3. theeleven (7 stars, 1 fork)
- 11 autonomous AI agents opening live football prop markets on X Layer
- Custom Uniswap v4 hook (PropMarketHook) — advanced DeFi primitive
- Live on X Layer mainnet (chain 196), contracts on OKLink
- 67/67 contract tests, 229/229 agent runtime tests, 172/172 web tests (100% coverage)
- Demo video on X, system status page

### 4. remlo (13 stars, 3 forks)
- Borderless payroll + agent payment infrastructure
- Multi-chain: Solana + Base/Tempo MPP + x402 + fiat rails
- ERC-8004 identity/reputation on Tempo, SAS on Solana
- Full Next.js app with employer/employee dashboards, API, admin

### 5. pact (1 star, 1 fork)
- Verifiable AI-as-a-Service on 0G mainnet
- Live on 0G mainnet (chainId 16661), first settled job on 2026-05-15
- 5 contracts, TEE-attested inference, commit-reveal flow
- 5-minute demo video on YouTube, full deploy guide

### 6. flow-guard (3 stars, 2 forks)
- BCH-native streaming/recurring payment protocol
- CashScript covenants on Bitcoin Cash UTXOs
- Live on flowguard.cash, Docker Compose deploy

### 7. usance (3 stars, 1 fork)
- Capital operations for tokenized assets on X Layer + Hedera
- 17 contract tests + 36 TS tests, `make test-differential` (Solidity + TS + Rust agree)
- 22 frozen canonical scenarios verified to the wei
- 13-page spec in spec/ (invariants.md, risk-model.md, evidence-model.md, etc.)

### 8. keeperhub-flightcheck (1 fork)
- Proves KeeperHub can execute onchain from any environment
- Every run recorded as verifiable evidence with tx hashes + block numbers
- "Onboarding now ends with an onchain fact"

---

## The winning patterns

### PATTERN 1: "What Is Actually Real" — verifiable claims section
The single most dominant pattern. Every winning repo has a README section that explicitly separates:
- What is verified live (with contract addresses, tx hashes, explorer links, exact commands to re-verify)
- What is NOT verified (honest disclosure of gaps)

Examples:
- bull-rush `docs/submission/TESTING-STATUS.md`: explicitly lists "Zero external playtesters", "migration not deployed to production", "no live relayer, no funded season, no on-chain grid"
- routedock `agent/RUN_RESULTS.md`: date, network, agent address, starting balance, every tx hash with explorer links
- usance README: "What is actually real" section — "Every claim below is reproducible from a fresh clone" with table of claim → `make verify-integrations` / `make test-differential`
- keeperhub-flightcheck: every run has a tx hash, block number, challenge ID, proof capsule directory

**Why it wins:** Judges can verify claims with zero trust in the builder. This converts skepticism into confidence. It also surfaces the *hardest* truth: what ISN'T working. That honesty + specificity is a rare combination.

**How to copy:** Every README must have a "What is actually real" or "What IS verified" section. For every claim ("live on mainnet", "100% test coverage"), include the exact command or tx hash to re-verify. Be ruthlessly honest about gaps.

### PATTERN 2: Real on-chain deployments with source verification
Almost every repo deploys to mainnet/testnet and verifies contracts on the explorer. Not "planned" or "demo screenshots" — actual deployed, source-verified contracts with real addresses.

Evidence of depth:
- bull-rush: BOT Chain mainnet (677), 3 contracts, all source-verified on BOTScan. Deployment doc reads back bytecode length via `eth_getCode`, constructor state via `cast call`, verification status via explorer API
- theeleven: X Layer mainnet (196), PropMarketHookFactory + USDT0 addresses, OKLink verification
- remlo: Tempo moderato (chainId 42431) + Solana devnet, 7+ contracts with explorer links
- pact: 0G mainnet (16661), 7 contracts, first settled job tx `0xbb36752d…` live on chainscan
- usance: X Layer mainnet contracts, LayerZero V2 endpoint IDs, Chainlink feed reads all verifiable via `make verify-integrations`

**Common chain IDs that appear repeatedly (builders should note):**
- BOT Chain mainnet = 677 (or 0x2a5)
- Tempo Moderato = 42431
- X Layer mainnet = 196, X Layer testnet = 1952
- 0G mainnet = 16661
- Base Sepolia = 84532
- Solana devnet (standard)

**Why it wins:** "Live on mainnet" is a trust-but-verify claim. Source verification + on-chain state reads turn it into a provable fact. Judges see a real address, click through to a real explorer, and the contract is actually there.

**How to copy:** Deploy to a real chain. Use a chain with cheap/free testnet (Stellar, Tempo, X Layer, BOT Chain). Source-verify on the explorer. Include the exact deploy tx hash and a `cast call` snippet to read back constructor state. Re-verify from chain rather than trusting the deploy tool output.

### PATTERN 3: Commit-reveal + cryptographic honesty as a primitive
This is the technical signature. winsznx doesn't just build "smart contracts" — he builds systems where the protocol enforces that the builder cannot cheat the user.

Examples:
- bull-rush: `DailyGridRegistry.openGrid()` reverts if `dayId` already exists → "operator cannot reroll a day's course". `VerifiedRunRegistry` only the relayer writes, only after server re-simulates the replay. Client-claimed score is never trusted — only server's own re-derivation.
- pact: commit-reveal ordering is the moat — "buyer commits input hash on JobCreated → seller commits attestation+output_hash on submitAttestation BEFORE revealing output to buyer"
- theeleven: `PropMarketHook` uses commit-reveal so nobody can see the exact market line while staking. Each agent commits `keccak256(revealedParams || salt || agent)` on creation
- routedock: MPP session has monotonic cumulative check (voucher can only increase), dispute resolution with 17280-ledger refund window
- remlo: escrow PDA on Solana — even under a fully compromised server, funds remain claimable by the correct counterparty

**Why it wins:** This isn't "trust us, we verified it." The protocol makes trust unnecessary. Judges who understand crypto recognize this instantly. It shows the builder understands the medium, not just the marketing.

**How to copy:** Design one cryptographic honesty primitive into every project. Examples: a merkle root published once and immutable (bull-rush SeasonPrizeVault), a commit-reveal for game state or market params, a monotonic invariant enforced in SQL + contract + application layer (routedock), a covenanted recovery path (remlo). The primitive should make some operator action impossible by construction.

### PATTERN 4: Multi-chain from day one, unified abstraction
He doesn't pick one chain. He builds abstractions that work across 2-3 chains and settles on each.

Examples:
- remlo: Tempo MPP + x402 on Base, SPL on Solana + Streamflow. ERC-8004 on Tempo, SAS on Solana. Same API (`/api/mpp/*` and `/api/x402/*`) negotiates the right mode per request
- routedock: x402 on Stellar + MPP charge + MPP session — one `client.pay()` call, SDK auto-selects mode from provider manifest
- usance: X Layer mainnet core + Hedera institutional facility
- theeleven: X Layer mainnet for markets, ERC-3009 gasless staking, Flap WorldCupResolver on BNB Chain for outcome resolution

The pattern: deploy a core contract set on the primary chain, bridge/settle on secondary chains where the user/rail exists, and write ONE abstraction that handles the negotiation.

**How to copy:** Don't hardcode one chain. Design the payment/abstraction layer to negotiate. Use the 402 challenge pattern (routedock's 402 response lists all payment options). Have at least 2 settlement paths even if one is "primary + testnet."

### PATTERN 5: Monorepo with workspaces, pinned toolchain, CI gates
All major repos are pnpm + turborepo monorepos with:
- `packageManager` field pinning (pnpm 9.15.9, 10.33.0, 11.21.0 observed)
- `engines: { node: ">=20" }` or specific node version
- workspace-filtered scripts (`pnpm --filter @pkg/name test`)
- Version-pinned deps (pact CLAUDE.md lists exact: node 20.18.0, pnpm 9.12.3, typescript 5.6.3, etc.)

CI is treated as a verification gate, not just a build:
- bull-rush CI: gitleaks (full history), typecheck, `sim:check` (drift detection), `npm test`, `sim:test` (cross-process fingerprint), `forge test` (43), integration tests (real Postgres+Redis+anvil), production build
- routedock: `pnpm verify` (build + typecheck + tests + bundles), `test-differential` (Solidity + TS agree on 22 scenarios)
- usance: `make test` runs contract tests + TS tests + Rust tests; `test-differential` proves Solidity and TypeScript agree to the wei on frozen fixtures

**The `sim:check` / drift-detection pattern (bull-rush):** The deterministic game engine exists in two places — `src/sim/` (client) and `server/src/sim/` (server). `sim:check` is a non-mutating check that fails if they've drifted. `sim:test` includes a cross-process fingerprint check proving two separate OS processes produce identical results. This runs in CI AND as part of `npm run build`.

**How to copy:** Use pnpm workspaces + turbo. Pin exact versions in package.json. Write a `verify`/`verify --fast` script that runs the same gates CI does, run it on pre-push. Add a drift-detection check if you have shared logic between client/server. Make CI fail loudly on any mismatch, never silently.

### PATTERN 6: "Test-differential" — cross-engine / cross-language invariant testing
This is a standout testing pattern. Instead of just unit tests, he writes frozen canonical scenarios and proves multiple implementations agree.

- usance: `make test-differential` — Solidity, TypeScript, and Rust reference engine all produce identical results on 22 frozen scenarios. Fixtures are committed and checked for drift. The `gen_fixtures.py` script regenerates them from `spec/accounting.md`.
- pact: `PactEscrowInvariants.t.sol`, `PactRegistryInvariants.t.sol`, `ReputationVaultInvariants.t.sol` — invariant test files alongside unit tests
- bull-rush: cross-process fingerprint (`1183386224`) proving determinism
- theeleven: 67/67 contract tests, 229/229 agent runtime, 172/172 web — all listed in skill.md frontmatter as verifiable claims

**How to copy:** Write at least one cross-implementation invariant test. If you have a Solidity contract, also implement the business logic in TypeScript and assert they agree on frozen fixtures. If you have a game engine, run it twice in separate processes and compare fingerprints. Include a `test-differential` target and run it in CI.

### PATTERN 7: Honest testing-status disclosure (the antidote to hacker lies)
bull-rush's `docs/submission/TESTING-STATUS.md` is a masterclass in honest disclosure. It's deliberately unflattering:

> "No one outside this project has played the rewritten game. No testers recruited..."
> "trybullrush.xyz currently serves the pre-migration build... is not what a visitor to the production URL gets today."
> "No live relayer, no funded season, no on-chain grid."

This is NOT a weakness in the submission — it's a strength. It tells the judge: "I know exactly what isn't done, and I'm telling you before you find out." This builds enormous trust because it's the opposite of the typical "everything works perfectly, 10/10" submission.

**How to copy:** Create a TESTING-STATUS or "What Is Actually Real" doc. List what IS tested with exact numbers (213 passing, 4 suites). Then list what is NOT tested/verified with brutal honesty. Update it as you build. Judges will respect the honesty and you won't get caught in a lie during Q&A.

### PATTERN 8: Skill manifests (ERC-8257-style) for agent composability
The theeleven repo ships `packages/mcp-server/skill.md` — a YAML-frontmatter agent skill manifest:

```yaml
schema_version: "1.0"
spec_compatibility: "ERC-8257-style agent skill manifest"
id: "regista11.stake.v1"
...
network:
  chain_id: 196
contracts:
  factory: "0x080627e..."
  settlement_token: "0x779Dd..."
  pool_manager: "0x360E..."
runtime:
  module: "@regista11/x402-facilitator"
trust:
  on_chain_proofs:
    - "0xeff55..."  # day-0 EIP-3009 settlement
  test_coverage:
    contracts: "67/67 · 100% line/branch/function"
```

The manifest declares capability surface (4 tools + 2 resources), prerequisites, error codes, and on-chain proofs. It's "compatible, not certified" — honest about spec compliance level.

**Why it wins:** In 2026, hackathon judges are looking for agent-native composability. Shipping a skill manifest means an autonomous agent can consume your protocol without a browser. This is the "built-for-agents" credential.

**How to copy:** If your project has any on-chain action an agent could trigger, ship a `skill.md` manifest. Define: ID (reverse-DNS), network (chain_id + RPC + explorer), contracts (addresses), runtime module, on-chain proofs (tx hashes), test coverage numbers, and a capability surface table. Make it MCP-compatible.

### PATTERN 9: Deterministic engine with off-chain verification + on-chain receipt
bull-rush is the purest example. The game engine is deterministic (seed + version + ruleset hash). The server re-simulates every replay independently. Only the server's result is recorded on-chain via `VerifiedRunRegistry`. The contracts don't re-run the sim (too expensive in Solidity) — they receive the already-verified result and make it tamper-evident.

Key insight: "Solidity cannot re-run the deterministic sim cheaply, so verification stays off-chain and this is the receipt of it." He split verification (off-chain, expensive) from attestation (on-chain, cheap) deliberately.

The `sim:sync` + `sim:check` tooling ensures the client and server engines never drift. `sim:test` includes a cross-process fingerprint check.

**How to copy:** If your project has any client-side computation that determines an outcome, make it deterministic. Re-simulate server-side. Record only the verified result on-chain. Ship drift-detection tooling. Write a cross-process fingerprint test.

### PATTERN 10: Asset provenance discipline + build-time compliance gates
bull-rush ships `ASSET_PROVENANCE.md` (status of every audio/visual asset) and `scripts/check-forbidden-assets.mjs` (fails the build if any unlicensed filenames are present). The production soundtrack is "not included" and "must not be deployed until replaced."

CI explicitly runs `npm run check-assets` and the README admits: "A fresh CI checkout never has the unlicensed local music files, so this passes here by construction — it exists to catch a future deploy or commit that reintroduces them."

**Why it wins:** Shows professional-grade compliance awareness. Judges (especially enterprise partners like Stellar, 0G, BNB) care about licensing. This prevents a win from being revoked post-judging.

**How to copy:** If you include any third-party assets (music, images, fonts), create an asset provenance doc. Add a build-time check script that fails if forbidden assets are present. Document the license status of every asset explicitly.

---

## Tech stack patterns

### Frontend (by repo)
- **bull-rush**: React 19 + React Three Fiber + three.js + postprocessing (bloom) + zustand + Vite → Cloudflare Pages (static)
- **remlo**: Next.js (Next 15) + Tailwind + Privy auth + wagmi + RainbowKit, Vercel
- **theeleven**: Next.js + Tailwind + Vitest, Railway/Windsor
- **pact**: Next.js 15 + React 19 + Tailwind 4.0.0-beta + wagmi 2.13 + RainbowKit 2.2, Vercel
- **usance**: Next.js + Playwright e2e tests, pnpm workspaces

**Wallet stack (consistent):** wagmi + RainbowKit 2.x + WalletConnect Cloud projectId (required env var). bull-rush uses ONLY the bare `injected()` connector — no optional metaMask()/walletConnect() modules that carry audit findings.

**Deployment targets:**
- Frontend: Vercel (default), Cloudflare Pages, Cloudflare Workers (routedock migrated FROM Vercel TO Cloudflare Workers via OpenNext adapter)
- Backend: Railway (bull-rush Hono API), Supabase (remlo DB), Railway (pact indexer)

### Smart contracts
- **Framework**: Foundry (forge) across all repos. solc 0.8.24–0.8.28, evm_version cancun, optimizer enabled (200 runs)
- **Via IR**: pact uses `via_ir = true`; bull-rush and theeleven do NOT (via_ir=false for bull-rush, omitted for theeleven)
- **Conventions**: NatSpec on every external function, checks-effects-interactions, nonReentrant, named custom errors (not revert strings), immutable/immutables for contract references, events for all state changes
- **Upgradeability**: remlo uses UUPS upgradeable for ERC-8004 registries; most others are non-upgradeable (immutable references preferred)
- **OpenZeppelin**: SafeERC20, SafeTIP20 (custom for Tempo), MerkleProof, ERC7857 INFT (pact)

### Testing patterns
- **Forge**: `forge test -vv` with unit + fuzz + invariant tests. bull-rush: 43 tests. pact: separate `Invariants.t.sol` files per contract.
- **Integration**: bull-rush integration suite uses REAL Postgres + Redis + anvil (not mocks). remlo: 36 TypeScript tests + 17 contract tests. usance: 22 frozen scenarios compared across Solidity + TS + Rust.
- **Cross-process**: bull-rush sim tests include a fingerprint (`1183386224`) proving two OS processes produce identical results.
- **End-to-end local**: bull-rush `npm run local:verify:grid` plays a run, submits it, submits a tampered one, asserts accept/reject. usance `make demo-local` → http://localhost:3000/simulate with frozen scenarios.
- **Flightcheck pattern** (keeperhub): every environment onboarding ends with an onchain transaction — deploy canary contract, execute, verify receipt. "Onboarding now ends with an onchain fact."

### Architecture conventions
- **Multi-contract design**: Never one big contract. bull-rush (3), pact (5), remlo (5+ + erc8004/3), theeleven (factory + hook + resolver), usance (EvidenceRegistry + PassportRegistry + RiskPolicyRegistry...)
- **Role separation**: owner / scheduler / relayer split (bull-rush). Owner sets config; hot key executes; contracts can't grant more power to hot keys.
- **Permissionless claims**: SeasonPrizeVault.claim takes `account` as explicit parameter — self-claim AND relayer claim use the same code path (merkle proof authorizes the pair, funds always go to `account`).
- **Merkle trees**: SeasonPrizeVault uses merkle root published once, claimed with merkle proof.
- **Versioned migrations**: remlo uses `schema_migrations` table (not `CREATE TABLE IF NOT EXISTS` at boot). API refuses to start against unmigrated DB.

### Documentation conventions
- **CLAUDE.md / AGENTS.md** as project rules (pact has 100-line CLAUDE.md with hard rules, pinned stack, invariants — "Loaded every Claude Code session")
- **Deployment evidence docs** (bull-rush MAINNET-DEPLOYMENT.md, theeleven DEPLOYMENT.md in contracts/)
- **Spec documents** (usance has 13 spec docs; pact has MASTER_PRD.md, AGENT_PROGRESS.md)
- **Agent integration guides** (theeleven has agent-integration.md + agent-onchainos-integration.md with full skill manifest)
- **CHANGELOG.md** with changeset format (routedock uses .changeset/)
- **CONTRIBUTING.md** with exact prerequisites, versions, build order

### CI/CD patterns
- **Concurrency groups**: bull-rush cancels in-flight CI on new push (`concurrency: group: ci-${{ github.ref }}`)
- **Pre-push hooks**: routedock runs `pnpm verify --fast` on push (not commit — slow build makes per-commit bypass too easy)
- **Secrets scanning**: bull-rush runs gitleaks on full git history (fetch-depth: 0), not just current state
- **Asset checks**: bull-rush CI runs `npm run check-assets` to catch forbidden/unlicensed files
- **Drift checks**: bull-rush `sim:check` ensures client/server engines haven't diverged
- **Smoke tests**: pact DEPLOY.md includes curl-based route checks (all 8 routes must return 200)

---

## The 6-phase submission checklist (synthesized from all WINNING repos)

1. **Pre-broadcast gates** — a single `release:check` / `verify` script that runs everything CI does (bull-rush runs 10 gates: secret scan, types, sim:check, unit tests, sim tests + fingerprint, forge fmt, forge test, integration, production build). Run it manually before every broadcast. Never deploy without it green.

2. **Contract source-verification + state readback** — after deploy, read back bytecode length via `eth_getCode`, constructor state via `cast call`, cross-contract wiring, and query the explorer API for `is_verified`. Don't trust the deploy tool's output. Document everything with tx hashes + explorer links.

3. **On-chain evidence with tx hashes** — every claim ("live", "settled", "executed") gets a verifiable tx hash. bull-rush includes deploy txs + gas costs. routedock includes 4 testnet tx hashes with explorer links. pact includes the first settled job tx `0xbb36752d…`.

4. **Honest testing-status disclosure** — a TESTING-STATUS.md that lists what IS tested (213 passing, 4 suites) AND what is NOT (zero external playtesters, not deployed to production). Never hide gaps.

5. **Skill manifest for agent consumption** — a `skill.md` YAML-frontmatter file declaring chain_id, contract addresses, runtime module, on-chain proofs (tx hashes), test coverage numbers, and capability surface. Make the protocol consumable by autonomous agents, not just humans.

6. **Reproducible from fresh clone** — `make doctor` + `make bootstrap` + `make test` + `make demo-local` must all work with zero prior machine state. usance has this exactly; pact has DEPLOY.md + DEMO_SCRIPT.md with pre-flight checklist.

---

## Quick tactical checklist to start winning like winsznx

[ ] Every repo: README has "What is actually real" section with tx hashes, contract addresses, explorer links, and `make verify` / re-check commands
[ ] Deploy at least one contract to a real chain (testnet at minimum) — BOT Chain 677, Tempo 42431, X Layer 196, or 0G 16661 are all cheap/free
[ ] Source-verify on the explorer. Prove it was verified by querying the API, not just trusting the submit response
[ ] 3-contract minimum design (registry/hook + executor/vault + proof/receipt), NEVER one big contract
[ ] Commit-reveal or merkle-root pattern somewhere the protocol can't cheat the user
[ ] pnpm + turborepo + pinned versions. `packageManager` field. Exact node version in engines
[ ] CI runs: gitleaks, typecheck, forge fmt --check, forge test, integration with real deps, build
[ ] `test-differential` target proving Solidity + TS agree on frozen scenarios
[ ] `TESTING-STATUS.md` with honest disclosure of what ISN'T tested (be deliberately unflattering)
[ ] `skill.md` manifest with ERC-8257-style frontmatter: chain_id, contracts, on_chain_proofs, test_coverage
[ ] `make doctor` / `bootstrap` / `demo-local` targets that work from a fresh clone with no prior state
[ ] Asset provenance doc + build-time script that fails if unlicensed assets are present
[ ] Pre-push hook running `verify --fast`. Build-time drift detection if you have shared client/server logic
