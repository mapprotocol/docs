# Installation Guide

## Overview

This guide covers installing MAP Relay Chain (Atlas) node software on Linux systems.

## Prerequisites

### System Requirements

- **OS**: Ubuntu 20.04 or 22.04 LTS
- **CPU**: 4+ cores
- **RAM**: 8+ GB
- **Storage**: 500+ GB SSD
- **Network**: Stable internet connection

### Install Dependencies

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install required packages
sudo apt install -y build-essential git curl wget
```

### Install Go

Atlas requires Go 1.21 or higher:

```bash
# Download Go
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz

# Install Go
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz

# Add to PATH
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:$GOPATH/bin' >> ~/.bashrc
source ~/.bashrc

# Verify installation
go version
```

## Installation Methods

### Method 1: Build from Source (Recommended)

```bash
# Clone repository
git clone https://github.com/mapprotocol/atlas.git
cd atlas

# Checkout latest stable release
git checkout v1.0.0  # Replace with actual version

# Build
make atlas

# Verify build
./build/bin/atlas version
```

### Method 2: Download Binary

```bash
# Download latest release
wget https://github.com/mapprotocol/atlas/releases/download/v1.0.0/atlas-linux-amd64.tar.gz

# Extract
tar -xzf atlas-linux-amd64.tar.gz

# Move to bin
sudo mv atlas /usr/local/bin/

# Verify
atlas version
```

## Initial Configuration

### Create Data Directory

```bash
# Create directory
mkdir -p ~/.atlas

# Set permissions
chmod 700 ~/.atlas
```

## Create Systemd Service

For production nodes, use systemd for process management:

```bash
# Create service file
sudo tee /etc/systemd/system/atlas.service > /dev/null <<EOF
[Unit]
Description=Atlas Node
After=network.target

[Service]
Type=simple
User=$USER
ExecStart=/usr/local/bin/atlas \\
  --datadir /home/$USER/.atlas \\
  --networkid 22776 \\
  --syncmode full \\
  --http \\
  --http.addr 0.0.0.0 \\
  --http.port 8545 \\
  --http.api eth,net,web3
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

### Enable and Start Service

```bash
# Reload systemd
sudo systemctl daemon-reload

# Enable service
sudo systemctl enable atlas

# Start service
sudo systemctl start atlas

# Check status
sudo systemctl status atlas
```

### View Logs

```bash
# Follow logs
journalctl -u atlas -f

# View recent logs
journalctl -u atlas -n 100
```

## Firewall Configuration

Open necessary ports:

```bash
# P2P port
sudo ufw allow 30303/tcp
sudo ufw allow 30303/udp

# RPC port (if exposing)
sudo ufw allow 8545/tcp

# Enable firewall
sudo ufw enable
```

## Verify Installation

### Check Sync Status

```bash
# Attach to console
atlas attach ~/.atlas/atlas.ipc

# Check sync status
> eth.syncing

# Check block number
> eth.blockNumber

# Check peers
> admin.peers.length
```

### Check via RPC

```bash
# Check block number
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}' \
  http://localhost:8545
```

## Troubleshooting

### Node Not Starting

```bash
# Check logs for errors
journalctl -u atlas -n 50

# Common issues:
# - Port already in use
# - Insufficient permissions
# - Missing genesis file
```

### Not Finding Peers

```bash
# Add bootnodes manually
atlas --datadir ~/.atlas \
  --bootnodes "enode://xxx@ip:port"
```

### Sync Stuck

```bash
# Try different sync mode
atlas --datadir ~/.atlas --syncmode fast

# Or clean and restart
rm -rf ~/.atlas/atlas/chaindata
atlas --datadir ~/.atlas init ~/.atlas/genesis.json
```

## Next Steps

- [Run Full Node](./run-full-node.md)
- [Run Archive Node](./run-archive-node.md)
- [Run RPC Node](./run-rpc-node.md)
- [Become Validator](/validator/become-validator.md)
