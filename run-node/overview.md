# Run Node Overview

## Introduction

This guide covers running MAP Relay Chain (Atlas) nodes. Different node types serve different purposes in the network.

## Node Types

| Type | Purpose | Storage | Use Case |
|------|---------|---------|----------|
| Full Node | Validate and relay | Pruned state | General participation |
| Archive Node | Store full history | Full state | Historical queries |
| RPC Node | Serve API requests | Pruned/Full | DApp backends |
| Validator Node | Produce blocks | Pruned state | Network security |

## Quick Comparison

```
┌─────────────────────────────────────────────────────────────────┐
│                        Node Types                                │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Full Node          Archive Node        RPC Node                │
│  ┌─────────┐        ┌─────────┐        ┌─────────┐              │
│  │ Pruned  │        │  Full   │        │ Pruned/ │              │
│  │  State  │        │ History │        │  Full   │              │
│  └─────────┘        └─────────┘        └─────────┘              │
│       │                  │                  │                    │
│       ▼                  ▼                  ▼                    │
│  P2P Network        Query History      Serve APIs               │
│                                                                  │
│                     Validator Node                               │
│                     ┌─────────┐                                  │
│                     │ Produce │                                  │
│                     │ Blocks  │                                  │
│                     └─────────┘                                  │
│                          │                                       │
│                          ▼                                       │
│                    Earn Rewards                                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Hardware Requirements

### Minimum Requirements

| Component | Full Node | Archive Node | RPC Node |
|-----------|-----------|--------------|----------|
| CPU | 4 cores | 8 cores | 4 cores |
| RAM | 8 GB | 16 GB | 8 GB |
| Storage | 500 GB SSD | 2 TB SSD | 500 GB SSD |
| Network | 25 Mbps | 25 Mbps | 100 Mbps |

### Recommended Requirements

| Component | Full Node | Archive Node | RPC Node |
|-----------|-----------|--------------|----------|
| CPU | 8 cores | 16 cores | 8 cores |
| RAM | 16 GB | 32 GB | 16 GB |
| Storage | 1 TB NVMe | 4 TB NVMe | 1 TB NVMe |
| Network | 100 Mbps | 100 Mbps | 1 Gbps |

## Software Requirements

- **OS**: Ubuntu 20.04/22.04 LTS (recommended)
- **Go**: 1.21+
- **Git**: Latest version

## Network Information

### Mainnet

| Parameter | Value |
|-----------|-------|
| Chain ID | 22776 |
| RPC | https://rpc.maplabs.io |
| Explorer | https://maposcan.io |

### Testnet (Makalu)

| Parameter | Value |
|-----------|-------|
| Chain ID | 212 |
| RPC | https://testnet-rpc.maplabs.io |
| Explorer | https://testnet.maposcan.io |

## Quick Start

### 1. Install Atlas

```bash
# Clone repository
git clone https://github.com/mapprotocol/atlas.git
cd atlas

# Build
make atlas

# Verify installation
./build/bin/atlas version
```

### 2. Initialize Node

```bash
# Create data directory
mkdir -p ~/.atlas

# Initialize with genesis
./build/bin/atlas --datadir ~/.atlas init genesis.json
```

### 3. Start Node

```bash
# Start full node
./build/bin/atlas --datadir ~/.atlas \
  --networkid 22776 \
  --syncmode full
```

## Next Steps

- [Installation Guide](./install.md) - Detailed installation instructions
- [Node Types](./node-types.md) - Learn about different node configurations (Full Node, Archive Node, RPC Node)
- [Become Validator](../validator/become-validator.md) - Run a validator node
