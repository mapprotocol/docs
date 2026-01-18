# MAP Omnichain Service (MOS) Overview

## Introduction

MAP Protocol provides MAP Omnichain Service (MOS) to offer common modules needed by cross-chain DApps, lowering the threshold for building cross-chain applications. With MAP Protocol, developers building cross-chain swap DApps only need to focus on the swap logic itself.

## Core Components

### AssetVault

AssetVault module handles cross-chain asset transfers with the following features:

- **Trustless**: No privileged admin can touch user funds
- **Secure**: All asset operations (minting, burning) can only be triggered with cross-chain messages backed by valid cryptographic proofs
- **Bulletproof**: Smart contracts guard users' funds with Merkle proof verification against light client data

### Messenger

Messenger is an independent inter-chain program that:

- Listens to relevant events on source chains
- Builds Merkle proofs on the source chain's ledger
- Transmits event messages and proofs to the destination chain
- Prepays gas fees and gets rewards from applications

**Key Properties:**
- As long as one honest Messenger is working, all cross-chain messages can be transferred
- Malicious attacks by Messengers will not cause asset loss, only verification failure
- Messenger SDK is open to DApp developers

### Vault & Data

**On Source Chain:**
- Receives assets or data
- Triggers events for Messengers to listen to

**On Relay/Destination Chain:**
- Receives cross-chain messages from Messengers
- Routes verification requests to Light-Client
- Records instructions after verification completes

## Cross-Chain Transfer Flow

![MOS Architecture](./images/mcs_final.png)

### Example: Transfer 100 USDC from Ethereum to BSC

**Prerequisites:**
- Light clients deployed on all chains (MapLightClientOnEth, MapLightClientOnBsc, EthLightClientOnMap, BscLightClientOnMap)
- AssetVault contracts deployed on all chains
- Messengers continuously updating light clients

**Step 1: Lock on Source Chain**

Alice locks 100 USDC to AssetVault on Ethereum. After execution, a `Lock` event is emitted.

**Step 2: Relay to MAP Relay Chain**

1. Messenger spots the Lock event
2. Messenger builds Merkle proof for the event
3. Messenger submits transaction to AssetVault on MAP Relay Chain
4. AssetVault queries EthLightClientOnMap to validate the proof
5. If valid, mUSDC contract mints 100 mUSDC for Alice
6. In the same transaction, 100 mUSDC is burned and a `Burn` event is emitted

**Step 3: Release on Destination Chain**

1. Messenger spots the Burn event on MAP Relay Chain
2. Messenger builds Merkle proof
3. Messenger submits transaction to AssetVault on BSC
4. AssetVault queries MapLightClientOnBsc to verify the block header
5. If valid, AssetVault transfers 100 USDC to Alice

**Result:** Alice's 100 USDC has been transferred from Ethereum to BSC. Alice only sent one transaction; Messengers handled the rest trustlessly.

## Wrapped Assets

For assets moving to MAP Relay Chain, wrapped versions are created:
- `mUSDC`: Wrapped USDC from Ethereum, BSC, etc.
- Other wrapped tokens follow the same pattern

## Getting Started

- [Getting Started Guide](./getting-started.md)
- [MOS Message API](./mos-message.md)
- [Examples](./examples/)
