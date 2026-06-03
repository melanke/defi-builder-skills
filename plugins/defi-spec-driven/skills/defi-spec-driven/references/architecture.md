# Phase 3 — Architecture & Contract Design Spec
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Design the contract system structure — what contracts exist, how they interact, who can call what, and what lives on-chain vs off-chain.

Load: `PROTOCOL.md`, `STATE.md`, `economics/INVARIANTS.md`, `economics/ECONOMICS.md`
Output: `architecture/CONTRACTS.md`, `architecture/ACCESS-CONTROL.md`, `architecture/ONCHAIN-OFFCHAIN.md`

---

## Step 1: Define the contract system

Most non-trivial DeFi protocols consist of multiple contracts. Start by listing every contract and what it's responsible for.

**Design questions to answer**:
- Which contracts exist and what is each one responsible for?
- How do they interact — which contracts call which?
- What external protocols do they integrate with, and through which interfaces?
- What is the upgrade strategy for each contract (confirm or refine from `PROTOCOL.md`)?

**Split vs consolidate decision**: For each contract, decide whether to split functionality further or keep it as a single unit. Splitting improves organization and separation of concerns, but every cross-contract call costs gas — in hot paths like swaps or liquidations, that overhead is measurable. When the decision is to keep a contract large rather than pay the call overhead, document that decision explicitly with the reasoning.

**Use LLM for first draft**: Once you have the economics from Phase 2 defined, AI can generate a first-draft contract structure quickly — interface stubs, dependency diagrams, boilerplate for standard patterns. Use it to skip mechanical work and focus on the decisions that require judgment.

---

## Step 2: Define the access control model

Spell out completely who can call what. Underspecified access control is one of the most common sources of security issues in DeFi.

**Questions to answer for every privileged operation**:
- Which functions are `external` and callable by anyone?
- Which require ownership or a specific role?
- Who holds the owner key — EOA, multisig, DAO governance contract?
- What is the timelock period for sensitive changes (parameter updates, upgrades, fee changes)?
- Are there emergency functions? Who can trigger them?
- Can users still withdraw in an emergency paused state?

The last point is important and often missed. A pause mechanism that also blocks user withdrawals creates a new attack vector: the protocol team (or an attacker who compromises the pause key) can trap user funds.

**Use LLM to flag gaps**: AI is good at identifying missing role checks, centralization risks, and functions that should be restricted but aren't. Use it as a second pass after your own analysis. The judgment call of *who actually holds the keys* and how much centralization is appropriate at this stage — that reasoning belongs to you.

---

## Step 3: Define on-chain vs off-chain boundaries

Not everything needs to live in the contract. On-chain storage is expensive, and moving computation off-chain (verified by signatures or Merkle proofs) is often the right trade-off for complex systems.

**Questions to answer**:
- What data is stored on-chain vs off-chain?
- What off-chain data is required for on-chain verification — oracles, Merkle roots, signed payloads?
- What happens if the off-chain component goes down or produces incorrect data?
- What trust assumptions does the off-chain component introduce?

---

## Step 4: Populate the output files

### CONTRACTS.md

```markdown
# Contract System

## Contracts Overview

| Contract | Responsibility | Upgrade strategy | Size budget |
|---|---|---|---|
| [Name] | [What it does] | [Immutable / UUPS / Transparent] | [~Xkb] |

## Contract Interactions

```
[ContractA] → calls → [ContractB.functionName()]
[ContractB] → integrates → [ExternalProtocol via IInterface]
```

## Per-Contract Design Notes

### [ContractName]
- **Responsibilities**: [What this contract owns]
- **External calls**: [What it calls out to]
- **Split decision**: [Why split or consolidated at this size]
- **Upgrade strategy**: [Specific strategy and rationale]
- **Key design decisions**: [Non-obvious choices and why]
```

### ACCESS-CONTROL.md

```markdown
# Access Control Model

## Role Definitions

| Role | Held by | Can call | Timelock |
|---|---|---|---|
| Owner | [EOA / multisig address] | [functions] | [X hours/days] |
| [RoleName] | [who] | [functions] | [X hours/days] |
| Public | Anyone | [functions] | — |

## Key Holder Analysis
- **Owner key**: [Who controls it, what they can do, what safeguards exist]
- **Emergency key**: [Who controls pause/unpause, scope of what can be paused]
- **Governance**: [DAO / multisig / single key — and upgrade path to decentralization]

## Emergency Mechanisms
- **Pause scope**: [Which functions are paused — and critically, can users still withdraw?]
- **Pause trigger**: [Who can trigger, under what conditions]
- **Recovery process**: [Steps to deploy a fix and unpause]
- **User fund safety**: [Guarantee that pausing cannot trap user funds]

## Centralization Risks
[Any remaining centralization risks that are accepted, and the rationale for accepting them]
```

### ONCHAIN-OFFCHAIN.md

```markdown
# On-Chain / Off-Chain Boundaries

## On-Chain State
[What data lives on-chain and why]

## Off-Chain Components

| Component | Purpose | On-chain verification | Failure mode |
|---|---|---|---|
| [Oracle] | [Price feeds] | [Signature / TWAP check] | [Stale price revert] |
| [Keeper] | [Liquidations / rebalancing] | [State check on-chain] | [Position remains open] |
| [Indexer] | [Event indexing] | [Read-only, no on-chain trust] | [UI only, no protocol risk] |

## Trust Assumptions
[What off-chain components must be trusted and why that trust level is acceptable]
```

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

- `CONTRACTS.md` documents every contract, its responsibility, and its upgrade strategy
- `ACCESS-CONTROL.md` documents every privileged operation, who can call it, and what timelocks apply
- Emergency mechanism is documented including the explicit guarantee about user withdrawals during pause
- `ONCHAIN-OFFCHAIN.md` documents every off-chain dependency and its failure mode
- All open questions from Phase 3 are logged in `STATE.md`
- `STATE.md` Phase History updated: `[x] Phase 3 — Architecture & Contracts`

Transition: "Phase 3 complete. Say **threat model** to begin Phase 4."
