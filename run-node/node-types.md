# Node Types

This guide covers running different types of MAP Relay Chain (Atlas) nodes.

## Full Node

A full node validates and relays transactions, participating in the P2P network with moderate resource requirements.

### Running a Full Node

```bash
# Basic full node
atlas --datadir ./node console

# With network ID specified
atlas --datadir ./node --networkid 22776 --syncmode full
```

### Single-Node Network (Development)

For testing and development:

```bash
# Single node mode
atlas --datadir ./node --single console

# With HTTP RPC enabled
atlas --datadir ./node --single --http --http.addr "127.0.0.1" --http.port 7445 console
```

## Archive Node

An archive node stores complete blockchain history, including all historical states. It's useful for:

- Block explorers
- Historical data queries
- Research and analytics
- Auditing and compliance

### What is an Archive Node?

A full node only keeps recent states (last ~128 blocks) and prunes older data. An archive node stores every historical state after each block, trading disk space for quick access to historical data.

### Hardware Requirements

Archive nodes require significantly more storage:
- **Storage**: 2-4 TB SSD (and growing)
- **RAM**: 16+ GB recommended
- **CPU**: Faster CPU helps with initial sync

### Running an Archive Node

```bash
atlas --datadir ./node --syncmode "full" --gcmode "archive"
```

## RPC Node

An RPC node serves JSON-RPC API requests for decentralized applications (DApps).

### How RPC Nodes Work

RPC nodes use the JSON-RPC protocol to:
- Receive requests from client applications
- Query blockchain data
- Execute and broadcast transactions
- Return results in JSON format

### Running an RPC Node

```bash
# Basic RPC node
atlas --datadir ./node --syncmode "full" --http --http.addr "127.0.0.1" --http.port 7445

# With more API modules
atlas --datadir ./node --syncmode "full" \
  --http --http.addr "0.0.0.0" --http.port 8545 \
  --http.api eth,net,web3,txpool
```

### RPC Configuration Options

| Option | Description |
|--------|-------------|
| `--http` | Enable HTTP-RPC server |
| `--http.addr` | HTTP-RPC listen address |
| `--http.port` | HTTP-RPC port (default: 8545) |
| `--http.api` | APIs offered over HTTP-RPC |
| `--http.corsdomain` | Allowed CORS domains |

## Comparison

| Feature | Full Node | Archive Node | RPC Node |
|---------|-----------|--------------|----------|
| State Storage | Pruned | Complete | Pruned/Complete |
| Disk Usage | ~500 GB | 2-4 TB | ~500 GB |
| Historical Queries | Limited | Full | Depends on mode |
| API Access | Console | Console | HTTP/WS |
| Use Case | P2P participation | Data services | DApp backends |
