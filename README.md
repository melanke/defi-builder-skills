# defi-spec-driven

A spec-driven development skill for DeFi / Solidity protocols. Guides you through six specification phases before writing a single line of Solidity, then implements against that spec function-by-function with a per-function gate loop.

## What it does

Runs a structured process from idea to auditable implementation:

1. **Protocol Init** — name, category, network, upgrade strategy
2. **Phase 1 — Research** — canonical implementations, post-mortems, failure modes
3. **Phase 2 — Economic Design** — invariants, stress tests, oracle/MEV analysis
4. **Phase 3 — Architecture** — contract system, access control, on/off-chain boundaries
5. **Phase 4 — Threat Modeling** — per-function attack vectors and mitigations
6. **Phase 5 — Interface, Storage & Events** — REQ-* slugs, storage layout, events, errors
7. **Phase 6 — Test Specification** — fuzz targets, attack scenarios, unit test specs
8. **Project Setup** — bootstraps a Foundry project from `melanke/foundry-security-template`, bridges the spec into the project structure
9. **Implementation** — per-function execute loop (implement → test → gate → commit), with two audit passes per contract

## Scope limitation: greenfield only

**This skill supports greenfield projects only.** If you are modifying an existing protocol, the skill will stop at protocol init and tell you so.

Modifications to existing codebases require mapping deployed contract state, existing storage layout, and live invariants before specifying — that path is not covered in this version. Modification support is planned for a future version.

## Requirements

- Foundry installed (`forge`, `cast`)
- GitHub CLI (`gh`) for repo creation from template
- A `melanke/foundry-security-template` clone (or GitHub access to create from it)

## Usage

Say any of these to Claude Code:

```
initialize protocol       — start a new protocol from scratch
protocol research         — Phase 1
economic spec             — Phase 2
architecture spec         — Phase 3
threat model              — Phase 4
interface spec            — Phase 5
test spec                 — Phase 6
setup project             — bootstrap Foundry project from template
implement protocol        — begin implementation (auto-runs setup if no project yet)
```

## Template dependency

The implementation phase assumes `melanke/foundry-security-template` as the project base. This template provides:

- Pre-configured `foundry.toml` with security linters
- Chimera fuzzing structure: `Properties.sol`, `BeforeAfter.sol`, `CryticToFoundry.sol`, `TargetFunctions.sol`
- `INVARIANTS.md`, `KNOWN_ISSUES.md` scaffold
- Pre-commit hooks for `forge fmt`, `forge lint`, `slither`
- `AGENTS.md` with toolchain-specific gate check commands

Spec documents (everything under `.specs/`) are toolchain-agnostic and can be used independently of the template.

## License

CC-BY-4.0
