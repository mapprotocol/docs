# v2 Contracts (TSS)

MAP Protocol v2 uses TSS (Threshold Signature Scheme) for cross-chain asset custody. This page lists the contract addresses and vault information for v2.

> **Note**: Vault addresses may change with each epoch due to TSS key rotation. Use the [tss_getVault](../api/json-rpc/tss-rpc.md) RPC method to query the latest vault information.

## TSS Contracts on MAP Relay Chain

| Contract | Address | Description |
|----------|---------|-------------|
| TSS Manager | [0xf3Fa35B6e3753cFe88Da86c71B2283F75EB64BE9](https://explorer.mapprotocol.io/address/0xf3Fa35B6e3753cFe88Da86c71B2283F75EB64BE9) | Manages TSS key generation and signing |
| Maintainers | [0xBfb6B7d0d5Fc120703F7B57CC18157d79a50a7e5](https://explorer.mapprotocol.io/address/0xBfb6B7d0d5Fc120703F7B57CC18157d79a50a7e5) | Maintainer registration and management |
| Relay | [0x00004080D86e1077ce96E67C1B167fF105025307](https://explorer.mapprotocol.io/address/0x00004080D86e1077ce96E67C1B167fF105025307) | Cross-chain message relay |
| Vault Manager | [0xdfC3F894Fb30F3E2f81608829968959DB65FD13b](https://explorer.mapprotocol.io/address/0xdfC3F894Fb30F3E2f81608829968959DB65FD13b) | Manages cross-chain vault operations |
| Gas Service | [0x1De9C47ae0074F347d9fF6bfA39CDd3389322eAD](https://explorer.mapprotocol.io/address/0x1De9C47ae0074F347d9fF6bfA39CDd3389322eAD) | Cross-chain gas fee management |
| Registry | [0xf2C3a9b547875C48Ba8598C4973BAC71eDc4C34c](https://explorer.mapprotocol.io/address/0xf2C3a9b547875C48Ba8598C4973BAC71eDc4C34c) | Contract registry |
| View Controller | [0x8c98bA0a11Cbb0DB3C52e4CD91B0844B39BC1F11](https://explorer.mapprotocol.io/address/0x8c98bA0a11Cbb0DB3C52e4CD91B0844B39BC1F11) | View functions for querying state |

## Vault Addresses

### BTC Vault

| Epoch | Address | Status |
|-------|---------|--------|
| 1 | `bc1q86m6d8fmlvan8r9pypd4p592xtcpfkn6ezxj3t` | active |

### DOGE Vault

| Epoch | Address | Status |
|-------|---------|--------|
| 1 | `Coming soon` | - |

## Supported Chains (v2)

| Chain | Chain ID | Status |
|-------|----------|--------|
| BTC | - | Active |
| DOGE | - | Active |

## Query Vault Information

To get the latest vault addresses programmatically, use the `tss_getVault` RPC method:

```shell
# Query latest vault
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"tss_getVault","params":["latest"],"id":1}' \
  http://localhost:8545

# Query by epoch
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"tss_getVault","params":["0x1"],"id":1}' \
  http://localhost:8545
```

See [TSS RPC API](../api/json-rpc/tss-rpc.md) for more details.
