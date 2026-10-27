# Threat model

## Description

The Ethereum Execution Layer Specification (EELS): the specification for
Ethereum execution-layer clients, written as executable Python. Each fork is a
complete, self-contained package under `src/ethereum/forks/`. The repository
also contains the testing framework (`packages/testing`) and the tests
(`tests/`) that generate the reference fixtures every execution client
(Geth, Nethermind, Besu, Erigon, Reth, etc) is tested against. A bug in the
specification is a protocol bug that every client following it inherits, and a
bug in the tests can hide a client bug.

## Priorities

1. Forks live on mainnet (up to and including Osaka and its BPO forks).
2. The next scheduled fork (Amsterdam).
3. Forks in development (Bogota and later).
4. Tests and the testing framework, where a bug lets an invalid client
   behavior pass the generated fixtures.

## Out of scope

- Typos and editorial issues.
- Issues limited to running EELS itself as a local client, such as crashes in
  the `ethereum_spec_tools` sync or t8n tooling that do not reflect a protocol
  bug.
- General infrastructure, CI, and documentation issues.
- Bugs that are no longer applicable to mainnet.
- Performance issues, unless caused by a design flaw in the protocol (for
  example, mispriced gas).
- Attacks through the Engine API, JSON-RPC, or devp2p, which are not specified
  here.
- High-effort denial of service issues.

## Untrusted inputs

A node receives untrusted blocks and transactions from peers and users. This
includes block headers, transaction payloads and signatures, EVM bytecode and
calldata, RLP encodings, withdrawals, execution requests, blob transactions,
authorization lists, and block access lists. Anything an attacker can put in a
transaction or a block is untrusted.

## Severities

**Critical**

- Accepting an invalid block, or rejecting a valid one (consensus split).
- Creating, destroying, or stealing ETH.
- Bypassing signature, nonce, or balance checks.

**High**

- Ambiguities about the validity of a block or transaction.
- Gas mispricing that lets a block cheaply take down many nodes.
- State root or receipt mismatches reachable on mainnet.
- Tests that pass a client with a consensus-breaking bug for a live fork.

**Medium**

- Gas mispricing with a limited practical impact.
- Gas charge ordering that is visible but untested.
- Incorrect behavior that needs uncommon but reachable conditions.
- Tests that miss a consensus-breaking bug in the next fork.
- Issues in the test framework that lead to an incorrect or ineffective
  test.

**Low**

- Mismatched constants with little practical impact.
- Issues that need impossible conditions on mainnet or testnets.
- Issues in forks after the next fork.

## Reports

Each report should state the affected fork, the affected component (for
example, an opcode, a precompile, or transaction validation), how to trigger
the issue, and its impact. When practical, include a test written with the
testing framework under `tests/` that demonstrates the issue and a patch that
fixes it. The test must be realistic and not hardcode values or states that
cannot occur on mainnet.
