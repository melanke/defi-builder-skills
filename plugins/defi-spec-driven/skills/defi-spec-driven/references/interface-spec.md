# Phase 5 — Interface, Storage & Events Spec
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Formalize every external behavior precisely. Nothing new is decided here — the architectural decisions from Phase 3 become pre-conditions, which data lives on-chain becomes storage layout, what the system promises becomes revert conditions and events.

Load: `PROTOCOL.md`, `STATE.md`, `economics/INVARIANTS.md`, `architecture/CONTRACTS.md`, `architecture/ACCESS-CONTROL.md`, `threat-model/THREAT-MODEL.md`
Output: `interface/FUNCTIONS.md`, `interface/STORAGE.md`, `interface/EVENTS.md`

---

## Step 1: Specify every external function

For every `external` and `public` function, document:

```
REQ-{verb}-{noun}
Function: functionName(type param, type param) returns (type)
Contract: ContractName
Caller: [Public / Role]

Parameters:
  - param: [valid range, what triggers revert at boundary]

Pre-conditions (require statements):
  - [condition 1]
  - [condition 2]

Post-conditions (guaranteed after successful execution):
  - [what is true after this succeeds]
  - INV-* references: [which invariants must hold after this call]

Revert conditions:
  - [condition] → error CustomErrorName(params)

State mutations:
  - [variable]: [how it changes]

Events emitted:
  - EventName(indexed param, param) — [when]
```

### Slug naming rules

- Requirements: `REQ-{verb}-{noun}` — e.g. `REQ-withdraw-solvency`, `REQ-deposit-min-shares`
- Invariants: `INV-{noun}-{property}` — e.g. `INV-vault-solvency`, `INV-share-monotonic`
- No sequential numbers — they break when requirements are added or removed
- Slugs must appear in three places: this spec file, the implementation code comment, and the test file

When naming is slow: give the LLM the requirement description and ask it to generate a slug. Review and accept/adjust. Consistent naming is worth the 10 seconds.

### Generating the Solidity interface

Once the behavioral spec is written, ask the LLM to generate a first-draft Solidity interface file — function signatures, NatSpec comments, custom errors, events. The output is typically 80–90% correct. The remaining 10–20% are always the interesting cases: semantics the LLM interpreted differently than intended, edge cases in pre-conditions it didn't model. This is exactly why the spec exists — without it, the LLM's interpretation silently becomes the implementation.

---

## Step 2: Document storage layout

Storage layout is a security concern in proxy-upgradeable contracts, not just an organizational one. A botched upgrade that writes to the wrong storage slot has caused real protocol losses.

Document every storage variable with its slot:

```markdown
## StorageLayout — ContractName

| Slot | Variable | Type | Notes |
|---|---|---|---|
| 0 | totalAssets | uint256 | |
| 1 | totalShares | uint256 | |
| 2 | shares | mapping(address => uint256) | |
| 3 | feeRecipient | address | |
| 4 | feeBps | uint256 | max 10000 |
| 5–54 | __gap | uint256[50] | reserved for upgrades |

Gap policy: [What the gap is reserved for, what constraints future upgrades must respect]
Upgrade constraint: [What variables must never be reordered or removed]
```

Use LLM to spot potential slot collisions and to flag variables that probably belong in the gap rather than at fixed positions.

---

## Step 3: Specify events and custom errors

### Events

Events are the protocol's audit log. Define them before implementation:

- Every state transition that a user or indexer would want to observe should emit an event
- Events should include enough information to reconstruct state without querying the contract
- Index fields that will be queried: user addresses, token addresses, IDs

Poor event design is one of the most common sources of indexing pain later. Changing events after deployment means migration or documentation that diverges from reality.

```solidity
// In EVENTS.md — specify before writing:
event Deposit(
    address indexed caller,
    address indexed owner,
    uint256 assets,
    uint256 shares
);
// Emitted by: deposit() — after shares are minted and assets transferred
// Indexed: caller (for user queries), owner (for delegated deposits)
```

### Custom errors

Since Solidity 0.8.4, custom errors are the preferred way to revert. Define each error with the function and condition that triggers it:

```solidity
// withdraw() — caller's share balance is below requested amount
error InsufficientBalance(uint256 requested, uint256 available);

// any restricted function — caller lacks the required role
error UnauthorizedCaller(address caller, bytes32 requiredRole);

// any oracle-dependent function — price feed hasn't updated within maxAge
error StalePriceData(uint256 updatedAt, uint256 maxAge);
```

### Error granularity policy

Each distinct error type adds to bytecode. If you decided in Phase 3 that a contract won't be split (to avoid cross-contract call overhead), you may be working within a tighter size budget near the 24KB limit. Set the policy here:

- **Granular**: Keep distinct error types where the caller genuinely needs to distinguish the failure
- **Consolidated**: For the rest, use a parametrized form like `error InvalidOperation(uint8 code)`

Set the policy before implementation. You won't know exact sizes until you compile, but the policy prevents inconsistency.

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

- Every `external` and `public` function has a REQ-* entry with pre/post-conditions, reverts, and state mutations
- Storage layout is documented slot-by-slot with gap policy
- Every event is defined with its indexed fields and emission condition
- Every custom error is defined with the function and condition that triggers it
- All REQ-* slugs are confirmed as unique and follow the naming convention
- `STATE.md` Phase History updated: `[x] Phase 5 — Interface, Storage & Events`

Transition: "Phase 5 complete. Say **test spec** to begin Phase 6."
