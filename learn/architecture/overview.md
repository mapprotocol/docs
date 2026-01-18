# MAP Protocol Architecture Overview

## Introduction

MAP Protocol is a peer-to-peer cross-chain infrastructure enabling secure interoperability between heterogeneous blockchains. The architecture is built on a three-layer design that separates concerns and provides flexibility.

## Three-Layer Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        Application Layer                                 │
│                                                                          │
│    Cross-chain DApps    │    Bridges    │    Omnichain Services         │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                     MOS (MAP Omnichain Service) Layer                    │
│                                                                          │
│    Message Passing    │    Asset Transfer    │    Verification          │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         Protocol Layer                                   │
│                                                                          │
│    MAP Relay Chain    │    Light Clients / TSS    │    Maintainers      │
└─────────────────────────────────────────────────────────────────────────┘
```

### Layer 1: Protocol Layer

The foundation providing core cross-chain verification and consensus.

| Component | Function |
|-----------|----------|
| MAP Relay Chain | Central relay hub and verification center |
| Light Clients | On-chain verification of other chains (v1) |
| TSS Network | Threshold signature for decentralized custody (v2) |
| Maintainers | Network participants maintaining cross-chain state |

### Layer 2: MOS Layer

The service layer providing cross-chain capabilities to applications.

| Component | Function |
|-----------|----------|
| Message Protocol | Standardized cross-chain message format |
| Messenger/Relayer | Off-chain services delivering messages |
| Vault/Gateway | Asset custody and verification |

### Layer 3: Application Layer

Where developers build omnichain applications.

**Examples:**
- Cross-chain DEX
- Omnichain NFT platforms
- Multi-chain lending protocols
- Cross-chain governance

## MAP Relay Chain

MAP Relay Chain (Atlas) is an EVM-compatible blockchain at the core of the protocol.

### Key Features

| Feature | Description |
|---------|-------------|
| Consensus | Istanbul BFT with ~5 second blocks |
| Compatibility | Fully EVM compatible |
| Native Token | MAPO for gas and staking |
| Finality | Instant finality with 2/3+ validator agreement |

### Functions

1. **Relay Hub**: Routes cross-chain messages
2. **Verification Center**: Hosts light clients and TSS contracts
3. **Governance**: Manages validators and protocol parameters

## Two Cross-Chain Solutions

MAP Protocol offers two complementary solutions:

### v1: Light Client Solution

Uses on-chain light clients for trustless verification.

```
Source Chain → Light Client → MAP Relay Chain → Light Client → Target Chain
```

**Best for:**
- Maximum trustlessness
- EVM-to-EVM communication
- Message passing

[Learn more about v1](./v1-light-client.md)

### v2: TSS Solution

Uses threshold signatures for decentralized custody.

```
Source Chain → Vault → Maintainer Network (TSS) → Gateway → Target Chain
```

**Best for:**
- Bitcoin integration
- Fast asset transfers
- Lower gas costs

[Learn more about v2](./v2-tss.md)

## Network Participants

| Role | Responsibility |
|------|----------------|
| **Validators** | Produce blocks on MAP Relay Chain |
| **Maintainers** | Update light clients (v1) or participate in TSS (v2) |
| **Messengers** | Relay cross-chain messages (v1) |
| **Relayers** | Submit signatures to target chains (v2) |
| **LP Providers** | Provide liquidity to Vaults (v2) |

## Supported Chains

MAP Protocol supports various blockchain types:

| Chain Type | Examples | v1 Support | v2 Support |
|------------|----------|------------|------------|
| EVM | Ethereum, BSC, Polygon | ✅ | ✅ |
| Non-EVM | Near, Solana | ✅ | ✅ |
| Bitcoin | BTC | ❌ | ✅ |

## Security Model

### Decentralization
- No single point of failure
- Distributed validation and signing
- Permissionless participation (v1) or staked participation (v2)

### Cryptographic Security
- Light client proofs based on source chain consensus
- TSS threshold signatures with 2/3 fault tolerance
- On-chain verification

### Economic Security
- Staking requirements
- Slashing for misbehavior
- Incentive alignment

## Getting Started

Depending on your role:

| Role | Getting Started |
|------|-----------------|
| User | Use cross-chain bridges built on MAP Protocol |
| Developer | [Build cross-chain apps](/develop/overview.md) |
| Validator | [Run a validator node](/validator/become-validator.md) |
| Maintainer (v1) | [Run Compass](/compass/overview.md) |
| Maintainer (v2) | [Run Compass-TSS](/compass-tss/overview.md) |
