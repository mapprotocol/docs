# MAP Relay Chain

MAP Relay Chain (Atlas) is an EVM-compatible blockchain at the core of MAP Protocol.

## Mainnet

| Property | Value |
|----------|-------|
| Network Name | MAP Mainnet |
| Chain ID | `22776` |
| Currency Symbol | MAPO |
| RPC URL | `https://rpc.maplabs.io` |
| WebSocket | `wss://wss.maplabs.io` |
| Explorer | https://explorer.mapprotocol.io |
| Explorer (alt) | https://maposcan.io |

### Add to Wallet

```json
{
  "chainId": "0x58f8",
  "chainName": "MAP Mainnet",
  "nativeCurrency": {
    "name": "MAPO",
    "symbol": "MAPO",
    "decimals": 18
  },
  "rpcUrls": ["https://rpc.maplabs.io"],
  "blockExplorerUrls": ["https://explorer.mapprotocol.io"]
}
```

## Testnet (Makalu)

| Property | Value |
|----------|-------|
| Network Name | MAP Makalu |
| Chain ID | `212` |
| Currency Symbol | MAPO |
| RPC URL | `https://testnet-rpc.maplabs.io` |
| WebSocket | `wss://testnet-wss.maplabs.io` |
| Explorer | https://testnet.maposcan.io |
| Faucet | https://faucet.mapprotocol.io |

### Add to Wallet

```json
{
  "chainId": "0xd4",
  "chainName": "MAP Makalu",
  "nativeCurrency": {
    "name": "MAPO",
    "symbol": "MAPO",
    "decimals": 18
  },
  "rpcUrls": ["https://testnet-rpc.maplabs.io"],
  "blockExplorerUrls": ["https://testnet.maposcan.io"]
}
```

## Network Specifications

| Specification | Value |
|---------------|-------|
| Consensus | Istanbul BFT (IBFT) |
| Block Time | ~5 seconds |
| Finality | Instant (single block) |
| EVM Compatibility | Full |
| Gas Token | MAPO |
