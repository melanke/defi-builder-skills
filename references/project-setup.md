# Project Setup — Initialize from Security Template
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Bootstrap the Foundry project from the security template before writing any `.sol` files. Bridge the completed spec into the project structure.

Triggered automatically when `implement protocol` is called and no Foundry project exists yet. Also triggered explicitly by `setup project` or `initialize foundry`.

**Prerequisites**: Phases 1–6 must be complete. `.specs/` must exist with at minimum `INVARIANTS.md` and `THREAT-MODEL.md`.

After setup, read `AGENTS.md` in the project root — it contains all toolchain-specific instructions: available commands, gate checks per complexity level, how to fix fmt/lintspec/slither findings, and how to maintain `INVARIANTS.md`, `Properties.sol`, `CryticToFoundry.sol`, `BeforeAfter.sol`, and `KNOWN_ISSUES.md`. The skill handles the spec methodology; `AGENTS.md` handles the toolchain.

---

## Step 1: Check if project already exists

Look for `foundry.toml` or any `.sol` file in the working directory. If found, the project is already initialized — skip to Step 5 (bridge only).

If not found: proceed with full setup.

---

## Step 2: Create project from template

Derive the repo name from the protocol name: lowercase, hyphens for spaces (`TinyVault` → `tiny-vault`, `American Spend` → `american-spend`). Ask the developer to confirm if not obvious from context.

```bash
gh repo create <protocol-name> \
  --template melanke/foundry-security-template \
  --private \
  --clone
cd <protocol-name>
```

If the developer already has a local clone of the template:
```bash
# Alternative: copy from local template
cp -r /path/to/defi-template /path/to/protocol-name
cd <protocol-name>
git remote set-url origin <new-repo-url>
```

---

## Step 3: Install and verify baseline

Run each command and confirm it succeeds before moving to the next:

```bash
bash scripts/install-hooks.sh   # installs pre-commit hooks — must not be skipped
cp .env.example .env            # required even if empty — some scripts source it
forge install                   # generates foundry.lock — commit it alongside the next git commit
forge build                     # must compile clean before any changes
forge test                      # Counter tests must pass — baseline verified
```

If `install-hooks.sh` fails: check that the script exists and is executable (`chmod +x scripts/install-hooks.sh`). Do not proceed without hooks installed — they are the primary gate for fmt/lint/test on every commit.

If `forge build` or `forge test` fails: stop and investigate before proceeding. Do not bridge spec into a broken project.

---

## Step 4: Remove template examples

```bash
rm src/Counter.sol
rm src/interfaces/IExample.sol
rm script/Counter.s.sol
rm test/Counter.t.sol
rm test/unit/ExampleTest.t.sol
rm test/integration/ExampleIntegrationTest.t.sol
rm test/symbolic/CounterProof.t.sol
```

These are scaffolding examples. After removal, `forge build` will fail until real contracts are added — this is expected.

---

## Step 5: Bridge spec → project

This is the core of the setup. Read `.specs/` and populate the project files.

### 5a. Populate `INVARIANTS.md`

Read `.specs/economics/INVARIANTS.md`. For each INV-* entry, add to the project root `INVARIANTS.md` using the format:

```markdown
### INV-{noun}-{property}

**Statement:** [from spec]
**Plain language:** [from spec]
**Breaks when:** [from spec]
**Scope:** [ContractName — infer from architecture spec]
**Property function:** `property_{noun}_{property}()` in `test/recon/Properties.sol`
```

The project `INVARIANTS.md` header already says it's generated from `.specs/economics/INVARIANTS.md` — keep that note intact.

### 5b. Create property_ stubs in `test/recon/Properties.sol`

For each INV-* slug, add a stub function. Naming is mechanical: `INV-vault-solvency` → `property_vault_solvency()`.

```solidity
// INV-vault-solvency: totalAssets() >= totalSupply() * convertToAssets(1e18) / 1e18
// See .specs/economics/INVARIANTS.md and INVARIANTS.md
function property_vault_solvency() public pure returns (bool) {
    // TODO: implement — change to view when reading contract state
    return true;
}
```

One stub per INV-* entry. The comment includes the statement from the spec and the source file reference.

Remove the `property_example_stub()` placeholder once at least one real stub is added.

### 5c. Wire stubs in `test/recon/CryticToFoundry.sol`

Add one `assert()` per property_ function in `invariant_properties()`:

```solidity
function invariant_properties() public pure {
    // Change to view when property_ functions read contract state
    assert(property_vault_solvency());
    assert(property_share_monotonic());
    // one line per stub
}
```

Remove the `property_example_stub()` assert when replacing with real stubs.

### 5d. Add BeforeAfter variables

Read `.specs/interface/FUNCTIONS.md` and `.specs/economics/INVARIANTS.md`. For each state variable that invariants reason about, add a field to `BeforeAfter.sol`:

```solidity
struct Vars {
    uint256 vault_totalAssets;
    uint256 vault_totalSupply;
    // one field per variable that appears in invariant checks
}
```

Update `__before()` and `__after()` to capture these. Keep field names as `{contractName}_{variableName}` to avoid collisions in multi-contract setups.

### 5e. Seed `KNOWN_ISSUES.md`

Read `.specs/threat-model/THREAT-MODEL.md`. For each residual risk entry that is not explicitly "None" (i.e. any entry that names a remaining exposure, describes it as "Low", "acceptable because X", or similar), add a placeholder entry:

```markdown
### [Review pending] ContractName — [residual risk description]

**Source:** THREAT-MODEL.md — [function name]
**Status:** Pending Slither analysis — to be updated after first `forge build`

[paste the residual risk description from the threat model]
```

These placeholders flag known accepted risks for auditors before any static analysis has run. Update them with actual Slither detector names after the first analysis pass.

### 5f. Generate Solidity interface files

Read `.specs/interface/FUNCTIONS.md` and `.specs/interface/EVENTS.md`. For each contract, create `src/interfaces/I[ContractName].sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;

/// @title I[ContractName]
/// @notice [one-line description from PROTOCOL.md]
interface I[ContractName] {
    // -------------------------------------------------------------------------
    // Events — from .specs/interface/EVENTS.md
    // -------------------------------------------------------------------------

    /// @notice [from EVENTS.md]
    event EventName(address indexed param, uint256 value);

    // -------------------------------------------------------------------------
    // Errors — from .specs/interface/EVENTS.md
    // -------------------------------------------------------------------------

    /// @notice [condition that triggers this error]
    error ErrorName();

    // -------------------------------------------------------------------------
    // Functions — from .specs/interface/FUNCTIONS.md
    // -------------------------------------------------------------------------

    /// @notice [from FUNCTIONS.md plain language description]
    /// @param param_ [from FUNCTIONS.md parameter description]
    /// @return [from FUNCTIONS.md return description]
    function functionName(uint256 param_) external returns (uint256);
}
```

One file per contract. All NatSpec (`@notice`, `@param`, `@return`) lives here — the implementation uses `/// @inheritdoc I[ContractName]` and never duplicates it.

Remove `src/interfaces/IExample.sol` (the template placeholder) once the real interfaces are added.

### 5h. Generate unit, integration, and symbolic test stubs

Read `.specs/tests/UNIT-TESTS.md`, `.specs/tests/SCENARIOS.md`, and `.specs/tests/FUZZ-TARGETS.md`.

**Unit tests** — for each contract that has REQ-* entries in UNIT-TESTS.md, create `test/unit/[ContractName]Test.t.sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;

import {Test} from "forge-std/Test.sol";
import {[ContractName]} from "../../src/[ContractName].sol";

contract [ContractName]Test is Test {
    [ContractName] internal [contractName];

    function setUp() public {
        [contractName] = new [ContractName]();
    }

    // REQ-{slug}
    /// @dev Given: [precondition]. When: [action]. Then: [outcome].
    function test_Given_..._When_..._Then_...() public {
        // TODO: implement
    }
}
```

One function stub per scenario in UNIT-TESTS.md. Name from the Gherkin Given/When/Then. Comment with the REQ-* slug.

**Integration tests** — create `test/integration/[ProtocolName]IntegrationTest.t.sol` with one stub per scenario in SCENARIOS.md:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;

import {Test} from "forge-std/Test.sol";

contract [ProtocolName]IntegrationTest is Test {
    address internal alice = makeAddr("alice");
    address internal bob = makeAddr("bob");
    address internal carol = makeAddr("carol");

    function setUp() public {
        // TODO: deploy full system
    }

    // Scenario: [name from SCENARIOS.md]
    function test_Given_..._When_..._Then_...() public {
        // TODO: implement
    }
}
```

**Symbolic proofs** — for every INV-* classified as `symbolic` or `both` in FUZZ-TARGETS.md, add a `check_` stub to `test/symbolic/[ContractName]Proof.t.sol`. Rename or create the file per contract:

```solidity
// check_[invariant_slug_underscored] — INV-{slug}
// [assertion from FUZZ-TARGETS.md symbolic entry]
function check_[invariant_slug_underscored](/* relevant params */) public view {
    // TODO: implement — stub generated from spec
    // vm.assume guards from FUZZ-TARGETS.md go here
}
```

---

## Step 6: Verify the bridge

```bash
forge fmt --check   # formatting must be clean — run first
forge build         # may fail — contracts not written yet, that's expected
```

If `forge fmt --check` fails: run `forge fmt` (no flags) to auto-fix, then re-check.
If `forge build` fails only because contracts don't exist yet: expected. If it fails due to malformed Solidity in the recon files: fix before proceeding.

---

## Step 7: Create IMPL-TASKS.md and transition to implementation

Load `interface/FUNCTIONS.md` and create `.specs/impl/[ContractName]-IMPL-TASKS.md` per the [impl-tasks.md](impl-tasks.md) instructions.

Then begin the execute loop for the first function.

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

- Template is initialized and git hooks are installed
- Example files removed: `Counter.sol`, `IExample.sol`, `Counter.s.sol`, `Counter.t.sol`, `ExampleTest.t.sol`, `ExampleIntegrationTest.t.sol`, `CounterProof.t.sol`
- `INVARIANTS.md` is populated from `.specs/economics/INVARIANTS.md`
- `Properties.sol` has one `property_` stub per INV-* entry
- `CryticToFoundry.sol` wires all stubs in `invariant_properties()`
- `BeforeAfter.sol` captures the state variables invariants reason about
- `KNOWN_ISSUES.md` has placeholder entries for accepted residual risks
- `src/interfaces/I[ContractName].sol` exists for every production contract with full NatSpec
- `test/unit/[ContractName]Test.t.sol` has one stub per UNIT-TESTS.md scenario
- `test/integration/[ProtocolName]IntegrationTest.t.sol` has one stub per SCENARIOS.md scenario
- `test/symbolic/[ContractName]Proof.t.sol` has one `check_` stub per symbolic INV-*
- `forge fmt --check` passes
- `IMPL-TASKS.md` is created
