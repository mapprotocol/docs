# MAP Omnichain Service (MOS)

## Introduction

MAP Omnichain Service (MOS) provides common modules for building cross-chain DApps, lowering the development threshold. MOS supports two types of cross-chain operations:

- **Asset Cross-chain**: Transfer tokens between different blockchains
- **Message Cross-chain**: Call contracts or sync data across chains

## Core Components

### MOS Relay

MOS Relay is the main contract on MAP Relay Chain, responsible for:

- Processing cross-chain transfers from users
- Calling LightNodeManager to verify messages
- Forwarding transactions to other chain's MOS contracts
- Managing token minting/burning and Vault permissions
- Handling fee distribution

### MOS

MOS is the main contract on source/destination chains, responsible for:

- Processing cross-chain transfers from users
- Calling LightNode to verify MAP Relay Chain transactions
- Parsing cross-chain events

### Messenger

Messenger is an independent inter-chain program that:

- Listens to events on source chains
- Builds Merkle proofs on source chain's ledger
- Transmits messages and proofs to destination chain
- Prepays gas fees and earns rewards

**Key Properties:**
- As long as one honest Messenger works, all cross-chain messages can be transferred
- Malicious Messenger attacks only cause verification failure, not asset loss
- Messenger SDK is open to DApp developers

### Vault

Vault is the equity token contract for each cross-chain token:

- Stakes user liquidity, issues VToken
- Records cross-chain fees and distributes to liquidity providers
- Handles liquidity withdrawal and transfer

### Fee

Fee contract manages cross-chain fee collection and distribution:

- Sets fee distribution ratio for Vault, Relay, and Protocol
- Sets fee charging standards

## Proof Verification

Cross-chain data verification flow:

1. Verify Proof in transaction body
2. Prove transaction body can construct ReceiptRoot
3. Prove ReceiptRoot exists in block header
4. Verify header legitimacy against LightNode's stored headers

## Cross-chain Types

| Type | Description | Use Case |
|------|-------------|----------|
| [Asset Cross-chain](./asset-cross-chain.md) | Transfer tokens between chains | Token bridges, DEX |
| [Message Cross-chain](./message-cross-chain.md) | Call contracts or sync data | Omnichain DApps |

## Contract Addresses

See [v1 Contracts](../network/v1-contracts.md) for MOS contract addresses on supported chains.

## Getting Started

- [Asset Cross-chain Guide](./asset-cross-chain.md)
- [Message Cross-chain Guide](./message-cross-chain.md)
- [Build OmniApp Tutorial](./omni-app.md)
