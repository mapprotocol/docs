# Compass-TSS Requirements

## Overview

This document outlines the requirements for running a Compass-TSS node as a Maintainer in MAP Protocol v2.

## Hardware Requirements

### Minimum Specifications

| Component | Requirement |
|-----------|-------------|
| CPU | 4 cores, 2.0 GHz+ |
| RAM | 8 GB |
| Storage | 100 GB SSD |
| Network | 100 Mbps symmetric |

### Recommended Specifications

| Component | Requirement |
|-----------|-------------|
| CPU | 8+ cores, 3.0 GHz+ |
| RAM | 16 GB+ |
| Storage | 500 GB NVMe SSD |
| Network | 1 Gbps symmetric |

### Storage Considerations

- **Key Storage**: TSS key shares require secure storage
- **Database**: LevelDB for local state
- **Logs**: Sufficient space for operational logs

## Network Requirements

### Connectivity

- **Static IP**: Recommended for reliable P2P connections
- **Dynamic DNS**: Acceptable alternative to static IP
- **Uptime**: 99.9%+ availability expected

### Ports

| Port | Protocol | Purpose |
|------|----------|---------|
| 8080 | TCP | P2P communication |
| 9090 | TCP | Metrics (optional) |
| 26656 | TCP | MAP Relay Chain P2P |
| 8545 | TCP | JSON-RPC (if running validator) |

### Firewall Rules

```bash
# Allow P2P communication
ufw allow 8080/tcp

# Allow metrics (optional)
ufw allow 9090/tcp
```

### Latency Requirements

- **P2P Latency**: < 500ms to majority of Maintainers
- **RPC Latency**: < 100ms to MAP Relay Chain node

## Software Requirements

### Operating System

| OS | Version | Support |
|----|---------|---------|
| Ubuntu | 20.04 LTS, 22.04 LTS | Recommended |
| Debian | 11, 12 | Supported |
| CentOS | 8, 9 | Supported |
| macOS | 12+ | Development only |

### Dependencies

- Go 1.21+
- Git
- Make
- GCC (for CGO)

### Installation Commands (Ubuntu)

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install dependencies
sudo apt install -y git make gcc curl

# Install Go
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

## Prerequisites

### 1. MAP Relay Chain Validator

You must be running a MAP Relay Chain validator node:

- Synced to latest block
- Participating in consensus
- Sufficient stake locked

### 2. Validator Registration

Your validator must be registered and active:

```bash
# Check validator status
marker getValidator --address <your-validator-address>
```

### 3. Maintainer Registration

Register as Maintainer on MAP Relay Chain:

```bash
# Register as Maintainer (see register-maintainer.md for details)
```

### 4. Stake Requirements

| Requirement | Amount |
|-------------|--------|
| Validator Stake | 1,000,000 MAPO minimum |
| Maintainer Bond | Additional stake may be required |

## Security Requirements

### Key Management

- **Secure Storage**: Key shares must be encrypted at rest
- **Backup**: Secure backup of key shares (offline)
- **Access Control**: Restrict access to key files

### Server Security

- **SSH**: Key-based authentication only
- **Firewall**: Restrict to necessary ports
- **Updates**: Regular security updates
- **Monitoring**: Intrusion detection recommended

### Operational Security

- **Separate Accounts**: Don't run as root
- **Dedicated Server**: Avoid shared hosting
- **Physical Security**: Secure data center recommended

## Monitoring Requirements

### Recommended Monitoring

- CPU, RAM, Disk usage
- Network connectivity
- P2P peer count
- Signing participation rate
- Slash points accumulation

### Alerting

Set up alerts for:
- Node offline
- High resource usage
- Failed signing sessions
- Peer disconnections

## Checklist

Before proceeding to installation:

- [ ] Hardware meets minimum requirements
- [ ] Static IP or dynamic DNS configured
- [ ] Required ports open
- [ ] Operating system installed and updated
- [ ] Dependencies installed
- [ ] MAP Relay Chain validator running and synced
- [ ] Validator registered and active
- [ ] Sufficient stake locked
- [ ] Security measures in place
