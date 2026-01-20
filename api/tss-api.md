# TSS Cross-chain API

TSS cross-chain API returns TSS cross-chain data, and provides the following interfaces.

- [getChainTxHeight](#getChainTxHeight)
- [getOrderSetByChainHeight](#getOrderSetByChainHeight)
- [getRecordByOrderId](#getRecordByOrderId)
- [getRecordByTxHash](#getRecordByTxHash)
- [getChainPendingTxs](#getChainPendingTxs)

## getChainTxHeight

Get the current scan height of the chain based on the specified blockchain ID.

- **Method**: `GET`
- **Path**: `/cross/chain/height`
- **Describe**: Query the current scan height of a specified blockchain。

### Parameters
  | Parameter | Type | Requied |  Description |
  |-----------|------|-------------| ---- | 
  | `chainId` | string | Yes| Unique Identifier of Blockchain |

### Returns
    return string of chain height

### Example

- **Request**:
    ```bash
    curl -X GET "https://tss-api.chainservice.io/cross/chain/height?chainId=22776"
    ```
- **Response**:
    ```json
    {
      "code": 0,
      "data": {
        "height": "22099966"
      },
      "msg": "success"
    }
    ```


## getOrderSetByChainHeight

Based on the blockchain ID and block height, retrieve the set of all cross-chain transaction order IDs scanned at that height.

- **Method**: `GET`
- **Path**: `/cross/chain/height/orders`
- **Describe**: Query all cross-chain transaction orders that occurred at a specific block height.

### Parameters
  | Parameter | Type | Requied |  Description |
  |-----------|------|-------------| ---- |
  | chainId | string | Yes |   22776 |
  | height | string | Yes |  12245 |

### Returns
    `Array` - Array of string

### Example

- **Request**:
    ```bash
    curl -X 'GET' \
      'https://tss-api.chainservice.io/cross/chain/height/orders?chainId=22776&height=22099953' \
      -H 'accept: application/json'
    ```

- **Response**:
    ```json
    {
      "code": 0,
      "data": {
        "height": "22099966",
        "set": [
          "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
          "0xe2cb120be5c7c03c600e3a202a2b4a5bdf69410db5b0a67a2be4a7d2a81cb141"
        ]
      },
      "msg": "success"
    }
    ```

## getRecordByOrderId

Retrieve the complete cross-chain status record of a transaction using the unique ID of the cross-chain order.

- **Method**: `GET`
- **Path**: `/cross/order`
- **Describe**: Query detailed transaction information across the entire chain based on cross-chain order ID.

### Parameters
  | Parameter | Type | Requied |  Description |
  |-----------|------|-------------| ---- |
  | orderId | string | Yes | Unique Identifier for Cross-Chain Orders |


### Returns

#### CrossSignel
  - **CrossSet**
    A cross-chain transaction dataset describes the state of a cross-chain transaction across the entire chain.
    | Field | Type | Description |
    | :--- | :--- | :--- |
    | `src` | `CrossData` | src chain tx info |
    | `dest` | `CrossData` | dst chain tx info |
    | `map_dest` | `CrossData` | relay dst tx info |
    | `relay` | `CrossData` | relay tx info |
    | `status` | `string` | Cross-chain transaction status, the meaning of its numerical values can be found in the `status` enumeration. |
    | `order_id` | `string` | order id |
    | `now` | `integer` | Current timestamp. |

  - **CrossData**
    Transaction data details on a single chain.
    | Field | Type  | Description | 
    | :--- | :--- | :--- |
    | `chain` | `string` | chain id |
    | `tx_hash` | `string` | tx hash |
    | `height` | `integer` | tx on height | 
    | `log_index` | `integer` | log in block index |
    | `timestamp` | `integer` | tx timestamp |
    | `topic` | `string` | tx topic | 
    | `order_id` | `string` | tx orderId |
    | `chain_and_gas_limit` | `string` | Chain identifier and Gas limit combination information. |

  - **status**
    | Number | Name |
    | :--- | :--- | 
    | `0` | `StatusOfInit` | 
    | `1` | `StatusOfPending` |
    | `2` | `StatusOfSend` |
    | `3` | `StatusOfCompleted` |
    | `4` | `StatusOfFailed` | 

### Example

- **Request**: 
    ```shell
    curl -X 'GET' \
      'https://tss-api.chainservice.io/cross/order?orderId=0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd' \
      -H 'accept: application/json'
    ```
- **Response**:
    ```json
    {
      "code": 0,
      "data": {
        "data": {
          "src": {
            "tx_hash": "0x3851dfca6deef8095bce53a6b63230359d9dc3a9e944d5827ab36df387b2fea7",
            "topic": "0x2b44f5da40771b7770f8469714d202f01aa69c410bd79a5dd86f9562413c3fca",
            "height": 22099953,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 6,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802635,
            "is_memoized": false
          },
          "relay": {
            "tx_hash": "0x3851dfca6deef8095bce53a6b63230359d9dc3a9e944d5827ab36df387b2fea7",
            "topic": "0x2b44f5da40771b7770f8469714d202f01aa69c410bd79a5dd86f9562413c3fca",
            "height": 22099953,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 6,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802635,
            "is_memoized": false
          },
          "relay_signed": {
            "tx_hash": "0x3b26fe78cc8b3c336a68ece04dd08ffe1f03e0e2a69784b9e5f2fa4baf9b07cf",
            "topic": "0x0bbc5d146426ff8f68e7bcd94bd95e273fabd90609e118badb81428405e38107",
            "height": 22099955,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 11,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802645,
            "is_memoized": false
          },
          "dest": {
            "tx_hash": "0xa7186bd57ed3a5c7a7f3fc4651e33de1ab0939c6c08b9db25a1c19368335bc12",
            "topic": "0x8104943fdd0997a3240b59b381251572ac6ac81941e1af29845de70edca938a4",
            "height": 76128655,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 213,
            "chain": "56",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802651,
            "is_memoized": false
          },
          "map_dest": {
            "tx_hash": "0x394326d2b4a54b18f72aea913775b965b76b92a57d738a5bd989078ca3c3040b",
            "topic": "0x298a40641bd31f72c733761e0e85a6bd8a36909666ac2ed63a42c8015d025638",
            "height": 22099966,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 2,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802700,
            "is_memoized": false
          },
          "now": 0,
          "status": 3,
          "status_str": "completed",
          "order_id": ""
        }
      },
      "msg": "success"
    }
    ```

## getRecordByTxHash

Retrieve the complete record of the cross-chain order to which a single transaction belongs by using its hash value.

- **Method**: `GET`
- **Path**: `/cross/tx`
- **Describe**: Based on the transaction hash on any chain, trace back the entire cross-chain process associated with it.。

### Parameters
  | Parameter | Type | Requied |  Description |
  |-----------|------|-------------| ---- |
  | tx | string | Yes | Transaction hash on any chain |


### Returns
  [CrossSignel](#CrossSignel)

### Example

- **Request**: 
    ```shell
    curl -X 'GET' \
      'https://tss-api.chainservice.io/cross/tx?tx=0x3851dfca6deef8095bce53a6b63230359d9dc3a9e944d5827ab36df387b2fea7' \
      -H 'accept: application/json'
    ```
- **Response**:
    ```json
    {
      "code": 0,
      "data": {
        "data": {
          "src": {
            "tx_hash": "0x3851dfca6deef8095bce53a6b63230359d9dc3a9e944d5827ab36df387b2fea7",
            "topic": "0x2b44f5da40771b7770f8469714d202f01aa69c410bd79a5dd86f9562413c3fca",
            "height": 22099953,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 6,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802635,
            "is_memoized": false
          },
          "relay": {
            "tx_hash": "0x3851dfca6deef8095bce53a6b63230359d9dc3a9e944d5827ab36df387b2fea7",
            "topic": "0x2b44f5da40771b7770f8469714d202f01aa69c410bd79a5dd86f9562413c3fca",
            "height": 22099953,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 6,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802635,
            "is_memoized": false
          },
          "relay_signed": {
            "tx_hash": "0x3b26fe78cc8b3c336a68ece04dd08ffe1f03e0e2a69784b9e5f2fa4baf9b07cf",
            "topic": "0x0bbc5d146426ff8f68e7bcd94bd95e273fabd90609e118badb81428405e38107",
            "height": 22099955,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 11,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802645,
            "is_memoized": false
          },
          "dest": {
            "tx_hash": "0xa7186bd57ed3a5c7a7f3fc4651e33de1ab0939c6c08b9db25a1c19368335bc12",
            "topic": "0x8104943fdd0997a3240b59b381251572ac6ac81941e1af29845de70edca938a4",
            "height": 76128655,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 213,
            "chain": "56",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802651,
            "is_memoized": false
          },
          "map_dest": {
            "tx_hash": "0x394326d2b4a54b18f72aea913775b965b76b92a57d738a5bd989078ca3c3040b",
            "topic": "0x298a40641bd31f72c733761e0e85a6bd8a36909666ac2ed63a42c8015d025638",
            "height": 22099966,
            "order_id": "0x4acc386427ec855298c70442167cad7c51a614e1d2392c85013761296da490bd",
            "log_index": 2,
            "chain": "22776",
            "chain_and_gas_limit": "142967269125167041077142995715525384507308163214988674526560832",
            "timestamp": 1768802700,
            "is_memoized": false
          },
          "now": 0,
          "status": 3,
          "status_str": "completed",
          "order_id": ""
        }
      },
      "msg": "success"
    }
    ```

## getChainPendingTxs

Gets a list of cross-chain transaction hashes with a status of "Pending" on a specified chain.

- **Method**: `GET`
- **Path**: `/cross/pending/tx`
- **Describe**: Queries all cross-chain transactions on a given chain that have not yet been uploaded to the chain.

### Parameters

  | Parameter | Type | Requied |  Description |
  |-----------|------|-------------| ---- |
  | chainId | string | Yes | Transaction hash on any chain |


### Returns
    Array of transaction hashes.

### Example
- **Request**:
    ```shell
    curl -X 'GET' \
      'https://tss-api.chainservice.io/cross/pending/tx?chainId=1360095883558914' \
      -H 'accept: application/json'
    ```
- **Response**:
    ```json
    {
      "code": 0,
      "data": {
        "txs": ["8c2d4520b91d4407c542256d46eace870b012704e10712ad220619af6b195780"]
      },
      "msg": "success"
    }
    ```