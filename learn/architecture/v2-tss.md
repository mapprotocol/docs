# v2 TSS Solution

## Overview

Protocol v2 introduces TSS (Threshold Signature Scheme) based cross-chain infrastructure. It enables secure asset transfers across all chains, including Bitcoin, through decentralized threshold signatures.

## How It Works

```
Source Chain              Maintainer Network           Target Chain
     │                          │                           │
     │  1. User deposits        │                           │
     │     to Vault             │                           │
     │                          │                           │
     │  2. Maintainers          │                           │
     │     observe ────────────►│                           │
     │                          │                           │
     │                    3. TSS KeySign                    │
     │                          │                           │
     │                          │  4. Signature submitted   │
     │                          ├──────────────────────────►│
     │                          │                           │
     │                          │  5. Gateway verifies      │
     │                          │     and releases assets   │
     │                          │                           │
```

### Step-by-Step Process

1. **Deposit**: User deposits assets to Vault address on source chain
2. **Observe**: Maintainers detect and confirm the deposit
3. **Sign**: Maintainers collaboratively sign withdrawal transaction via TSS
4. **Submit**: Signature is submitted to target chain
5. **Release**: Gateway contract verifies signature and releases assets

## Key Components

### TSS (Threshold Signature Scheme)

Cryptographic technology that allows multiple parties to jointly sign without any single party holding the full key.

- **2/3 Threshold**: Requires 2/3 of Maintainers to sign
- **No Single Point of Failure**: No one can sign alone
- **Single Signature Output**: Looks like a normal signature on-chain

### Vault

TSS-controlled address that holds cross-chain assets.

- Managed by Maintainer network
- No single entity controls the funds
- Supports multiple chains including Bitcoin

### Gateway

Smart contract on each chain for signature verification.

- Verifies TSS signatures
- Executes withdrawals
- Handles TSS key updates

### Maintainer

Network participant that observes events and participates in signing.

- Must be a registered Validator
- Participates in KeyGen and KeySign
- Earns rewards for participation

## Security Model

### Threshold Security

- **2/3 Byzantine Fault Tolerance**: System remains secure with up to 1/3 malicious nodes
- **Distributed Custody**: Assets are never controlled by a single entity
- **Slashing**: Malicious behavior is penalized

### Why It's Secure

- **No Private Key Exposure**: Full key never exists in one place
- **Decentralized Signing**: Multiple independent parties required
- **Economic Security**: Maintainers stake assets at risk

## Supported Chains

- **Bitcoin**: Full support via TSS Vault
- **Ethereum & EVM Chains**: Gateway contract verification
- **Solana**: secp256k1 verification via Gateway
- **Any Chain**: Can support any chain that verifies signatures

## Advantages over v1

| Aspect | v1 (Light Client) | v2 (TSS) |
|--------|-------------------|----------|
| Bitcoin Support | ❌ | ✅ |
| Gas Cost | Higher (proof verification) | Lower (signature verification) |
| Implementation | Need light client per chain | Universal TSS |
| Speed | Depends on light client sync | Fast threshold signing |

## Fusion with Light Client

Protocol v2 can integrate light client verification for enhanced security:

- TSS provides fast finality
- Light client provides additional trustless verification
- Best of both worlds

## Use Cases

- **Bitcoin Cross-chain**: Bridge BTC to EVM chains
- **Asset Transfers**: Fast and secure token transfers
- **LP-based Liquidity**: Liquidity providers earn fees
- **Multi-chain DeFi**: Access liquidity across chains

## Getting Started

- **Become Maintainer**: See [Compass-TSS Guide](/compass-tss/overview.md)
- **Provide Liquidity**: Deposit assets to Vault
- **Use Cross-chain**: Interact with Gateway contracts

## Roles in v2

| Role | Responsibility | Reward |
|------|----------------|--------|
| Validator | Block production on MAP Relay Chain | Block rewards |
| Maintainer | TSS signing & observation | Cross-chain fees |
| Relayer | Submit signatures to target chains | Transaction fees |
| LP Provider | Provide liquidity to Vaults | Fee sharing |
