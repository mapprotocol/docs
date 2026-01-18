# TSS RPC API

TSS (Threshold Signature Scheme) related RPC methods for MAP Protocol v2.

## tss_getVault

Retrieves vault information for a specific epoch or the latest epoch.

### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `epoch` | `QUANTITY` or `String` | Epoch ID (hexadecimal) or `"latest"` for current epoch |

### Returns

`Array` - Array of vault objects:

| Field | Type | Description |
|-------|------|-------------|
| `epoch` | `QUANTITY` | The epoch number |
| `public_key` | `String` | TSS public key for this epoch |
| `status` | `String` | Vault status (`active`, `inactive`, etc.) |
| `chains` | `Array` | List of supported chain names |
| `addresses` | `Array` | Chain-specific vault addresses |

**addresses object:**

| Field | Type | Description |
|-------|------|-------------|
| `chain` | `String` | Chain name (e.g., `BTC`, `DOGE`) |
| `address` | `String` | Vault address on the chain |

### Example

**Request (by epoch ID):**
```shell
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"tss_getVault","params":["0x1"],"id":1}' \
  http://localhost:8545
```

**Request (latest):**
```shell
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"tss_getVault","params":["latest"],"id":1}' \
  http://localhost:8545
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    {
      "epoch": 1,
      "public_key": "0xdddc5378e96320f465f1bf8ab228c8d88ec4cff6a32f76ac066a9fd28cd614433f16b5fc49053ecd6d46a13948896f397fdc15cbb50a3a2092eda3379b536c8f",
      "status": "active",
      "chains": [
        "BTC",
        "DOGE"
      ],
      "addresses": [
        {
          "chain": "BTC",
          "address": "bc1q86m6d8fmlvan8r9pypd4p592xtcpfkn6ezxj3t"
        }
      ]
    }
  ]
}
```
