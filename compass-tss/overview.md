# Compass-TSS Overview

## Introduction

Compass-TSS is the off-chain client software for participating in MAP Protocol v2's TSS (Threshold Signature Scheme) network. It enables Validators to become Maintainers and participate in cross-chain signing operations.

## What is Compass-TSS?

Compass-TSS is a node software that:

- **Observes** cross-chain events on multiple chains
- **Participates** in TSS key generation (KeyGen)
- **Signs** cross-chain transactions collaboratively (KeySign)
- **Communicates** with other Maintainer nodes via P2P network

## System Requirements

### Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 4 cores | 8+ cores |
| RAM | 8 GB | 16+ GB |
| Storage | 100 GB SSD | 500 GB SSD |
| Network | 100 Mbps | 1 Gbps |

### Network Requirements

- Static IP or reliable dynamic DNS
- Open ports for P2P communication
- Low latency connection to other Maintainers

### Prerequisites

- Running MAP Relay Chain Validator node
- Registered as Validator with sufficient stake
- Registered as Maintainer on-chain

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Compass-TSS Node                            │
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │  Observer   │  │   Signer    │  │  P2P Node   │              │
│  │             │  │             │  │             │              │
│  │ - Watch     │  │ - KeyGen    │  │ - Discovery │              │
│  │   chains    │  │ - KeySign   │  │ - Messaging │              │
│  │ - Parse     │  │ - Key       │  │ - Broadcast │              │
│  │   events    │  │   storage   │  │             │              │
│  └─────────────┘  └─────────────┘  └─────────────┘              │
│         │                │                │                      │
│         └────────────────┼────────────────┘                      │
│                          │                                       │
│                   ┌──────┴──────┐                                │
│                   │   Database  │                                │
│                   │  (LevelDB)  │                                │
│                   └─────────────┘                                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
         │                 │                 │
         ▼                 ▼                 ▼
   ┌──────────┐     ┌──────────┐     ┌──────────┐
   │  Source  │     │   MAP    │     │  Other   │
   │  Chains  │     │  Relay   │     │Maintainer│
   │          │     │  Chain   │     │  Nodes   │
   └──────────┘     └──────────┘     └──────────┘
```

## Key Features

### Multi-Chain Observation

- Monitors events on all connected chains
- Parses deposit and withdrawal events
- Submits observations to MAP Relay Chain

### TSS Operations

- Participates in KeyGen when elected
- Signs transactions via TSS KeySign
- Securely stores key shares

### P2P Network

- Discovers other Maintainer nodes
- Exchanges TSS protocol messages
- Maintains reliable connections

## Rewards and Penalties

### Rewards

- Share of cross-chain transaction fees
- Proportional to participation in signing

### Penalties (Slashing)

| Violation | Consequence |
|-----------|-------------|
| Offline during KeySign | Slash points |
| Failed KeyGen participation | Jail period |
| Double signing | Severe slash + jail |
| Invalid observations | Slash points |

## Comparison with Compass (v1)

| Feature | Compass (v1) | Compass-TSS (v2) |
|---------|--------------|------------------|
| Role | Maintainer + Messenger | TSS Maintainer |
| Function | Light client updates | TSS signing |
| Registration | Permissionless | Must be Validator |
| Key Management | No keys | TSS key shares |
| Consensus | Independent | Threshold (2/3) |

## Quick Start

1. **Set up Validator**: Run MAP Relay Chain validator node
2. **Register**: Register as Maintainer on-chain
3. **Install**: Install Compass-TSS software
4. **Configure**: Set up configuration file
5. **Run**: Start Compass-TSS node

## Next Steps

- [Requirements](./requirements.md) - Detailed hardware and network requirements
- [Register as Maintainer](./register-maintainer.md) - How to register on-chain

## Support

For issues and questions:
- GitHub Issues: [compass-tss repository](https://github.com/mapprotocol/compass-tss)
- Discord: MAP Protocol community
