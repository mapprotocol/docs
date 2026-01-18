# MAP Protocol 1.0

## Overview

Protocol 1.0 is MAP Protocol's light client-based cross-chain solution. It enables trustless cross-chain communication by verifying transactions through on-chain light clients.

## How It Works

```
Source Chain              MAP Relay Chain              Target Chain
     │                          │                           │
     │  1. User initiates       │                           │
     │     cross-chain tx       │                           │
     │                          │                           │
     │  2. Maintainer updates   │                           │
     │     light client ───────►│                           │
     │                          │                           │
     │  3. Messenger relays     │                           │
     │     message + proof ─────┼──────────────────────────►│
     │                          │                           │
     │                          │  4. Light client verifies │
     │                          │     and executes          │
     │                          │                           │
```

### Step-by-Step Process

1. **Initiate**: User sends cross-chain transaction on source chain
2. **Update**: Maintainer updates light client with latest block headers
3. **Relay**: Messenger relays the message with Merkle proof to target chain
4. **Verify**: Target chain's light client verifies the proof and executes

## Key Components

### Light Client

A smart contract that stores minimal blockchain state (block headers) to verify transactions from another chain.

- Deployed on each connected chain
- Verifies Merkle proofs
- Provides trustless verification

To validate the cryptographic proof for cross-chain messages, a trusted root is required. Normally the cryptographic proof is the existence Merkle proof of a specific value in a Merkle tree (e.g., the Merkle Patricia Tree in Ethereum). The trusted root is the Merkle root included in block headers.

#### Light Client for PoW Chains

For Nakamoto consensus-like chains (e.g., Ethereum), new block headers can be easily verified following the consensus rules such as hash links and accumulated work. If the network won't re-org more than n blocks, then a light client only needs to preserve n+1 newest block headers to verify new block headers autonomously.

#### Light Client for PoS Chains

For Proof-of-Stake and BFT based blockchains, blocks are signed by a group of selected staked validators. By verifying digital signatures, the validity of a block can be easily checked. The set of validators could change over time, but new validators are proved by the old set via signatures. With only validator set information (staked weight, public keys), a light client can check new block headers and update itself efficiently.

#### Light Client of MAP Relay Chain

MAP Relay Chain adopts PoS and IBFT consensus with aggregate BLS signature over BN256 curve. This allows verification of all validators' signatures to be reduced to verifying one aggregated signature with one aggregated public key. As precompile contracts for BN256 are widely supported by EVM-compatible blockchains, gas consumption for maintaining MAP Relay Chain's light client is minimized.

### Maintainer

Off-chain service that keeps light clients updated.

- Monitors source chain blocks
- Submits block headers to light clients
- Anyone can run a Maintainer

### Messenger

Off-chain service that relays cross-chain messages.

- Detects cross-chain events
- Generates Merkle proofs
- Delivers messages to target chains
- Earns fees for successful delivery

### MOS (MAP Omnichain Service)

The messaging layer that standardizes cross-chain communication.

- Unified message format
- Cross-chain event emission
- Message verification interface

## Security Model

### Trust Assumptions

- Source chain consensus is secure
- At least one honest Maintainer keeps light client updated

### Why It's Secure

- **Cryptographic Verification**: Light client verifies actual blockchain proofs
- **No Trusted Third Party**: Verification is done on-chain
- **Permissionless**: Anyone can participate as Maintainer or Messenger

## Supported Chains

- Ethereum
- BSC (BNB Smart Chain)
- Polygon
- Near Protocol
- And more EVM-compatible chains

## Limitations

- Requires light client implementation for each chain
- Higher gas costs for proof verification
- Cannot support Bitcoin (no smart contracts)

## Use Cases

- Cross-chain messaging between DApps
- Token transfers between EVM chains
- Cross-chain governance voting
- Omnichain NFT transfers

## Getting Started

- **Build Cross-chain App**: See [Development Guide](/develop/cross-chain-app/overview.md)
