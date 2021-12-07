
## JSON RPC API Reference


### web3

#### web3_clientVersion

Returns the current client version.

##### Parameters
none

##### Returns

`String` - The current client version.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "atlas/v0.3.2-stable/linux-amd64/go1.17.1"
}
```

#### web3_sha3

Returns Keccak-256 (*not* the standardized SHA3-256) of the given data.

##### Parameters

1. `DATA` - the data to convert into a SHA3 hash.

##### Example Parameters
```js
params: [
  "0x68656c6c6f20776f726c64"
]
```

##### Returns

`DATA` - The SHA3 result of the given string.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

### net

#### net_version

Returns the current network id.

##### Parameters
none

##### Returns

`String` - The current network id.
- `"22776"`: Mainnet
- `"212"`:   Testnet
- `"213"`:   Devnet

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "22776"
}
```

#### net_listening

Returns `true` if client is actively listening for network connections.

##### Parameters
none

##### Returns

`Boolean` - `true` when listening, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": true
}
```



#### net_peerCount

Returns number of peers currently connected to the client.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the number of connected peers.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x4"
}
```

### eth

#### eth_gasPrice

Returns the current price per gas in wei.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the current gas price in wei.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_gasPrice","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x77359400"
}
```



#### eth_maxPriorityFeePerGas

Returns a fee per gas that is an estimate of how much you can pay as a priority fee, or "tip", to get a transaction included in the current block.

##### Parameters
none

##### Returns

`QUANTITY` - the estimated priority fee per gas.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_maxPriorityFeePerGas","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x3b9aca00"
}
```



#### eth_syncing

Returns an object with data about the sync status or `false`.


##### Parameters
none

##### Returns

`Object|Boolean`, An object with sync status data or `FALSE`, when not syncing:
  - `startingBlock`: `QUANTITY` - The block at which the import started (will only be reset, after the sync reached his head)
  - `currentBlock`: `QUANTITY` - The current block, same as eth_blockNumber
  - `highestBlock`: `QUANTITY` - The estimated highest block
  - `pulledStates`: `String` -already complete state
  - `knownStates`: `String` -already know state


startingBlock: QUANTITY - The block at which the import started (will only be reset, after the sync reached his head)
currentBlock: QUANTITY - The current block, same as eth_blockNumber
highestBlock: QUANTITY - The estimated highest block

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "currentBlock": "0x2bb",
    "highestBlock": "0x2bd",
    "knownStates": "0x0",
    "pulledStates": "0x0",
    "startingBlock": "0x22b"
  }
}
// Or when not syncing
{
  "id":1,
  "jsonrpc": "2.0",
  "result": false
}
```



#### eth_accounts

Returns a list of addresses owned by client.


##### Parameters
none

##### Returns

`Array of DATA`, 20 Bytes - addresses owned by the client.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": ["0x84d46b3055454646a419d023f73472561b6cf20f"]
}
```

#### eth_blockNumber

Returns the number of most recent block.

##### Parameters
none

##### Returns

`QUANTITY` - integer of the current block number the client is on.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x9ff"
}
```

#### eth_getBalance

Returns the balance of the account of given address.

##### Parameters
DATA, 20 Bytes - address to check for balance.
QUANTITY|TAG - integer block number, or the string "latest", "earliest" or "pending", see the default block parameter

##### Returns

`QUANTITY` - integer of the current block number the client is on.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x84d46b3055454646a419d023f73472561b6cf20f", "latest"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x0"
}
```

#### eth_getBlockByNumber

Returns information about a block by block number.

##### Parameters
`QUANTITY|TAG` - integer of a block number, or the string "earliest", "latest" or "pending", as in the default block parameter.
`Boolean` - If true it returns the full transaction objects, if false only the hashes of the transactions.

##### Returns

`Object` - A block object, or null when no block was found:
    - `number`: QUANTITY - the block number. null when its pending block.
    - `hash`: DATA, 32 Bytes - hash of the block. null when its pending block.
    - `parentHash`: DATA, 32 Bytes - hash of the parent block.
    - `nonce`: DATA, 8 Bytes - hash of the generated proof-of-work. null when its pending block.
    - `logsBloom`: DATA, 256 Bytes - the bloom filter for the logs of the block. null when its pending block.
    - `transactionsRoot`: DATA, 32 Bytes - the root of the transaction trie of the block.
    - `stateRoot`: DATA, 32 Bytes - the root of the final state trie of the block.
    - `receiptsRoot`: DATA, 32 Bytes - the root of the receipts trie of the block.
    - `miner`: DATA, 20 Bytes - the address of the beneficiary to whom the mining rewards were given.
    - `totalDifficulty`: QUANTITY - integer of the total difficulty of the chain until this block.
    - `extraData`: DATA - the “extra data” field of this block.
    - `size`: QUANTITY - integer the size of this block in bytes.
    - `gasLimit`: QUANTITY - the maximum gas allowed in this block.
    - `gasUsed`: QUANTITY - the total used gas by all transactions in this block.
    - `timestamp`: QUANTITY - the unix timestamp for when the block was collated.
    - `transactions`: Array - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter.
    - `baseFeePerGas`: QUANTITY - The base fee is the bare minimum you will be charged to send a transaction on the network.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x1", true],"id":1}}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "baseFeePerGas": "0x3b9aca00",
    "extraData": "0xd8820100846765746888676f312e31352e368664617277696e00000000000000f87ec0c080b8413628b60a5e8d7a3e8b20d7afafbaee473814f7c20a400bbfb57cd4b388e0962d4c44720c2b58aece9d82cd2807d524237170c041ef02326365c40d10cdffa80000f307b0a5abe24b01c5f800d88d5b350bc781723283f870feab4f683571b585447b4909b9bd72201aa5dd6d99a349e4dc6d660006c3808080",
    "gasLimit": "0x2fa31c5",
    "gasUsed": "0x0",
    "hash": "0xdddfede981cb797b5492be3b6a238db872ae3260bb19472f06dfa5b2214850dc",
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "miner": "0x2dc45799000ab08e60b7441c36fcc74060ccbe11",
    "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "nonce": "0x0000000000000000",
    "number": "0x1",
    "parentHash": "0x22edf1b785c14db4fb893e6b01e74ad9ce01ece81de7c4a49c0ee594852c70cc",
    "receiptsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    "size": "0x2c7",
    "stateRoot": "0x71e4afa51d013ff42245c45f840e14117d692befd8a7fa52354bf61480d84f12",
    "timestamp": "0x619da786",
    "totalDifficulty": "0x2",
    "transactions": [],
    "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
  }
}
```

#### eth_getBlockByHash

Returns information about a block by block hash.

##### Parameters
`DATA`, 32 Bytes - Hash of a block.
`Boolean` - If true it returns the full transaction objects, if false only the hashes of the transactions.

##### Returns

See [eth_getBlockByNumber](#eth_getBlockByNumber)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByHash","params":["0xdddfede981cb797b5492be3b6a238db872ae3260bb19472f06dfa5b2214850dc", true],"id":1}}'
```
Result See [eth_getBlockByNumber](#eth_getBlockByNumber)

#### eth_getCode

Returns code at a given address.

##### Parameters

1. `DATA`, 20 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter).

##### Returns

`DATA` - the code from the given address.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getCode","params":["0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b", "0x2"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```

#### eth_getStorageAt

Returns the value from a storage position at a given address. 

##### Parameters

1. `DATA`, 20 Bytes - address of the storage.
2. `QUANTITY` - integer of the position in the storage.
3. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`DATA` - the value at this storage position.

##### Example
Calculating the correct position depends on the storage to retrieve. Consider the following contract deployed at `0x295a70b2de5e3953354a6a8344e616ed314d7251` by address `0x391694e7e0b0cce554cb130d723a9d27458f9298`.

```
contract Storage {
    uint pos0;
    mapping(address => uint) pos1;
    
    function Storage() {
        pos0 = 1234;
        pos1[msg.sender] = 5678;
    }
}
```

Retrieving the value of pos0 is straight forward:

```js
curl -X POST --data '{"jsonrpc":"2.0", "method": "eth_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x0", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x00000000000000000000000000000000000000000000000000000000000004d2"}
```

Retrieving an element of the map is harder. The position of an element in the map is calculated with:
```js
keccack(LeftPad32(key, 0), LeftPad32(map position, 0))
```

This means to retrieve the storage on pos1["0x391694e7e0b0cce554cb130d723a9d27458f9298"] we need to calculate the position with:
```js
keccak(decodeHex("000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"))
```
The geth  console which comes with the web3 library can be used to make the calculation:
```js
> var key = "000000000000000000000000391694e7e0b0cce554cb130d723a9d27458f9298" + "0000000000000000000000000000000000000000000000000000000000000001"
undefined
> web3.sha3(key, {"encoding": "hex"})
"0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9"
```
Now to fetch the storage:
```js
curl -X POST --data '{"jsonrpc":"2.0", "method": "eth_getStorageAt", "params": ["0x295a70b2de5e3953354a6a8344e616ed314d7251", "0x6661e9d6d8b923d5bbaab1b96e1dd51ff6ea2a93520fdc9eb75d059238b8c5e9", "latest"], "id": 1}' localhost:8545

{"jsonrpc":"2.0","id":1,"result":"0x000000000000000000000000000000000000000000000000000000000000162e"}

```

#### eth_call

Executes a new message call immediately without creating a transaction on the block chain.

##### Parameters
1. `Object` - The transaction call object
  - `from`: `DATA`, 20 Bytes - (optional) The address the transaction is sent from.
  - `to`: `DATA`, 20 Bytes  - The address the transaction is directed to.
  - `gas`: `QUANTITY`  - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
  - `gasPrice`: `QUANTITY`  - (optional) Integer of the gasPrice used for each paid gas
  - `maxFeePerGas`: `QUANTITY` - It's just the sum of baseFeePerGas and maxPriorityFeePerGas: maxFeePerGas = baseFeePerGas + maxPriorityFeePerGas.
  - `maxPriorityFeePerGas`: `QUANTITY` - When you submit a transaction you will also provide a "tip" to the miner. This is the maxPriorityFeePerGas field. fork.
  - `value`: `QUANTITY`  - (optional) Integer of the value sent with this transaction
  - `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
  - `data`: `DATA`  - (optional) Hash of the method signature and encoded parameters
  - `input`: `DATA`  - (optional) We accept "data" and "input" for backwards-compatibility reasons. "input" is the newer name and should be preferred by clients.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`DATA` - the return value of executed contract.

##### Example
```json
// Request
curl -X POST --data '{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "eth_call",
    "params": [
        {
            "from": "0xd13fe09e7a304709b1c4ed6bd3a2d6c272357bbb",
            "to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567",
            "gas": "0x76c0",
            "gasPrice": "0x9184e72a000",
            "value": "0x9184e72a",
            "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
        }
    ],
}'
// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x"
}
```

#### eth_estimateGas

Generates and returns an estimate of how much gas is necessary to allow the transaction to complete. The transaction will not be added to the blockchain. Note that the estimate may be significantly more than the amount of gas actually used by the transaction, for a variety of reasons including EVM mechanics and node performance.

##### Parameters

See [eth_call](#eth_call) parameters, expect that all properties are optional. If no gas limit is specified geth  uses the block gas limit from the pending block as an upper bound. As a result the returned estimate might not be enough to executed the call/transaction when the amount of gas is higher than the pending block gas limit.

##### Returns

`QUANTITY` - the amount of gas used.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_estimateGas","params":[{"from": "0xd13fe09e7a304709b1c4ed6bd3a2d6c272357bbb","to": "0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas": "0x76c0","gasPrice": "0x9184e72a000","value": "0x9184e72a","data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"}, "latest"],"id":1}],"id":1}'


// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x5498"
}
```

#### eth_getBlockTransactionCountByNumber

Returns the number of transactions in a block matching the given block number.

##### Parameters

1. `QUANTITY|TAG` - integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).

##### Returns

`QUANTITY` - integer of the number of transactions in this block.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByNumber","params":["0x1"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x0"
}
```

#### eth_getBlockTransactionCountByHash

Returns the number of transactions in a block from a block matching the given block hash.

##### Parameters

1. `DATA`, 32 Bytes - hash of a block.

##### Returns

`QUANTITY` - integer of the number of transactions in this block.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByHash","params":["0xdddfede981cb797b5492be3b6a238db872ae3260bb19472f06dfa5b2214850dc"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x0"
}
```

#### eth_getTransactionByHash

Returns the information about a transaction requested by transaction hash.


##### Parameters

1. `DATA`, 32 Bytes - hash of a transaction

##### Returns

`Object` - A transaction object, or `null` when no transaction was found:

  - `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
  - `blockNumber`: `QUANTITY` - block number where this transaction was in. `null` when its pending.
  - `from`: `DATA`, 20 Bytes - address of the sender.
  - `gas`: `QUANTITY` - gas provided by the sender.
  - `gasPrice`: `QUANTITY` - gas price provided by the sender in Wei.
  - `maxFeePerGas`: `QUANTITY` - It's just the sum of baseFeePerGas and maxPriorityFeePerGas: maxFeePerGas = baseFeePerGas + maxPriorityFeePerGas.
  - `maxPriorityFeePerGas`: `QUANTITY` - When you submit a transaction you will also provide a "tip" to the miner. This is the maxPriorityFeePerGas field. fork.
  - `hash`: `DATA`, 32 Bytes - hash of the transaction.
  - `input`: `DATA` - the data send along with the transaction.
  - `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
  - `to`: `DATA`, 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
  - `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block. `null` when its pending.
  - `value`: `QUANTITY` - value transferred in Wei.
  - `accessList`:
  - `chainId`:
  - `v`: `QUANTITY` - ECDSA recovery id
  - `r`: `DATA`, 32 Bytes - ECDSA signature r
  - `s`: `DATA`, 32 Bytes - ECDSA signature s

  - `payer`: `DATA`, 20 Bytes - address of the payer.
  - `fee`: `QUANTITY` - transaction fee in Wei.
  - `pv`: `QUANTITY` - ECDSA recovery id
  - `pr`: `DATA`, 32 Bytes - ECDSA signature pr
  - `ps`: `DATA`, 32 Bytes - ECDSA signature ps


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0x88df016429689c079f3b2f6ad39fa052532c56795b733da78a91ebe6a713944b"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "blockHash": "0xe1773ed82b29bb3ee541966e4a8d26c0dc2da8efe8534eb51d0522566a744b0f",
    "blockNumber": "0x824",
    "from": "0xd13fe09e7a304709b1c4ed6bd3a2d6c272357bbb",
    "gas": "0x4b6ed",
    "gasPrice": "0xd09dc300",
    "maxFeePerGas": "0x10c388d00",
    "maxPriorityFeePerGas": "0x9502f900",
    "hash": "0x9b9e8bab3acd3c581bb3231c6f5d3a13dc57feb69ab8767929669e8a08185c9b",
    "input": "0x608060405234801561001057600080fd5b506104a4806100206000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c806306fdde031461003b578063c47f002714610059575b600080fd5b610043610075565b60405161005091906102b2565b60405180910390f35b610073600480360381019061006e9190610230565b610103565b005b6000805461008290610388565b80601f01602080910402602001604051908101604052809291908181526020018280546100ae90610388565b80156100fb5780601f106100d0576101008083540402835291602001916100fb565b820191906000526020600020905b8154815290600101906020018083116100de57829003601f168201915b505050505081565b806000908051906020019061011992919061011d565b5050565b82805461012990610388565b90600052602060002090601f01602090048101928261014b5760008555610192565b82601f1061016457805160ff1916838001178555610192565b82800160010185558215610192579182015b82811115610191578251825591602001919060010190610176565b5b50905061019f91906101a3565b5090565b5b808211156101bc5760008160009055506001016101a4565b5090565b60006101d36101ce846102f9565b6102d4565b9050828152602081018484840111156101ef576101ee61044e565b5b6101fa848285610346565b509392505050565b600082601f83011261021757610216610449565b5b81356102278482602086016101c0565b91505092915050565b60006020828403121561024657610245610458565b5b600082013567ffffffffffffffff81111561026457610263610453565b5b61027084828501610202565b91505092915050565b60006102848261032a565b61028e8185610335565b935061029e818560208601610355565b6102a78161045d565b840191505092915050565b600060208201905081810360008301526102cc8184610279565b905092915050565b60006102de6102ef565b90506102ea82826103ba565b919050565b6000604051905090565b600067ffffffffffffffff8211156103145761031361041a565b5b61031d8261045d565b9050602081019050919050565b600081519050919050565b600082825260208201905092915050565b82818337600083830152505050565b60005b83811015610373578082015181840152602081019050610358565b83811115610382576000848401525b50505050565b600060028204905060018216806103a057607f821691505b602082108114156103b4576103b36103eb565b5b50919050565b6103c38261045d565b810181811067ffffffffffffffff821117156103e2576103e161041a565b5b80604052505050565b7f4e487b7100000000000000000000000000000000000000000000000000000000600052602260045260246000fd5b7f4e487b7100000000000000000000000000000000000000000000000000000000600052604160045260246000fd5b600080fd5b600080fd5b600080fd5b600080fd5b6000601f19601f830116905091905056fea264697066735822122038d2501678007ade07a8a41476358bf19cd27e0f7ab6ced37416d4929d0507c164736f6c63430008070033",
    "nonce": "0x5",
    "to": null,
    "transactionIndex": "0x0",
    "value": "0x0",
    "type": "0x2",
    "accessList": [],
    "chainId": "0x58f8",
    "v": "0x0",
    "r": "0x285d9e04a338ba42bef91b35d2eb252a470b626dd4e93c530fc7b60b912bbcb9",
    "s": "0x1fafb42d72545deffb953d409ff44b68cff40a07454dab346df64c91f5e386fd"
  }
}
```


#### eth_getTransactionByBlockNumberAndIndex

Returns information about a transaction by block number and transaction index position.

##### Parameters

1. `QUANTITY|TAG` - a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).
2. `QUANTITY` - the transaction index position.

##### Returns

See [eth_getTransactionByHash](#eth_gettransactionbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}'
```

Result see [eth_getTransactionByHash](#eth_gettransactionbyhash)


#### eth_getTransactionByBlockHashAndIndex

Returns information about a transaction by block hash and transaction index position.

##### Parameters

1. `DATA`, 32 Bytes - hash of a block.
2. `QUANTITY` - integer of the transaction index position.

##### Returns

See [eth_getTransactionByHash](#eth_gettransactionbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}'
```

Result see [eth_getTransactionByHash](#eth_getTransactionbyhash)

#### eth_getTransactionCount

Returns the number of transactions *sent* from an address.

##### Parameters

1. `DATA`, 20 Bytes - address.
2. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

##### Returns

`QUANTITY` - integer of the number of transactions send from this address.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionCount","params":["0xD13fE09E7A304709B1C4ed6Bd3a2D6C272357BBb","latest"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x6"
}
```

#### eth_getTransactionReceipt

Returns the receipt of a transaction by transaction hash.

**Note** That the receipt is not available for pending transactions.


##### Parameters

1. `DATA`, 32 Bytes - hash of a transaction

##### Example Parameters
```js
params: [
   '0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238'
]
```

##### Returns

`Object` - A transaction receipt object, or `null` when no receipt was found:

    - `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in.
    - `blockNumber`: `QUANTITY` - block number where this transaction was in.
    - `contractAddress `: `DATA`, 20 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
    - `cumulativeGasUsed `: `QUANTITY ` - The total amount of gas used when this transaction was executed in the block.
    - `effectiveGasPrice ` : `QUANTITY ` - effective gas price
    - `from`: `DATA`, 20 Bytes - address of the sender.
    - `to`: `DATA`, 20 Bytes - address of the receiver. null when it's a contract creation transaction.
    - `gasUsed `: `QUANTITY ` - The amount of gas used by this specific transaction alone.
    - `logs`: `Array` - Array of log objects, which this transaction generated.
    - `logsBloom`: `DATA`, 256 Bytes - Bloom filter for light clients to quickly retrieve related logs.
    - `transactionHash `: `DATA`, 32 Bytes - hash of the transaction.
    - `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block.
    - `type`: `QUANTITY` - transaction type, (0: LegacyTxType, 1: AccessListTxType, 2: DynamicFeeTxType)

It also returns _either_ :

    - `root` : `DATA` 32 bytes of post-transaction stateroot (pre Byzantium)
    - `status`: `QUANTITY` either `1` (success) or `0` (failure)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionReceipt","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "blockHash": "0xfbc61080bf5e44762b35b340d520bf729c68612ea40a8b87bfaa3120cef6901c",
    "blockNumber": "0x264b",
    "contractAddress": null,
    "cumulativeGasUsed": "0x5208",
    "effectiveGasPrice": "0x4190ab00",
    "from": "0xd13fe09e7a304709b1c4ed6bd3a2d6c272357bbb",
    "gasUsed": "0x5208",
    "logs": [],
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "status": "0x1",
    "to": "0xf0c8898b2016afa0ec5912413ebe403930446779",
    "transactionHash": "0xf15351ba1239fcc4452b507808cf4ec184e3c599f915eb783d05c719cf6afce4",
    "transactionIndex": "0x0",
    "type": "0x2"
  }
}
```

#### eth_sendTransaction

Creates new message call transaction or a contract creation, if the data field contains code.

##### Parameters
1. `Object` - The transaction object
    - `from`: `DATA`, 20 Bytes - (optional) The address the transaction is sent from.
    - `to`: `DATA`, 20 Bytes  - The address the transaction is directed to.
    - `gas`: `QUANTITY`  - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`: `QUANTITY`  - (optional) Integer of the gasPrice used for each paid gas
    - `maxFeePerGas`: `QUANTITY` - It's just the sum of baseFeePerGas and maxPriorityFeePerGas: maxFeePerGas = baseFeePerGas + maxPriorityFeePerGas.
    - `maxPriorityFeePerGas`: `QUANTITY` - When you submit a transaction you will also provide a "tip" to the miner. This is the maxPriorityFeePerGas field. fork.
    - `value`: `QUANTITY`  - (optional) Integer of the value sent with this transaction
    - `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
    - `data`: `DATA`  - (optional) Hash of the method signature and encoded parameters
    - `input`: `DATA`  - (optional) We accept "data" and "input" for backwards-compatibility reasons. "input" is the newer name and should be preferred by clients.

##### Example Parameters
```js
params: [{
  "from": "0xf675187ff5b76d2430b353f6736aa051253118ee",
  "to": "0xD13fE09E7A304709B1C4ed6Bd3a2D6C272357BBb",
  "gas": "0x76c0",
  "gasPrice": "0x4190ab00",
  "value": "0xde0b6b3a7640000",
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [eth_getTransactionReceipt](#eth_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example
```json
// Request
curl -X POST --data '{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "eth_sendTransaction",
    "params": [
        {
            "from": "0xf675187ff5b76d2430b353f6736aa051253118ee",
            "to": "0xD13fE09E7A304709B1C4ed6Bd3a2D6C272357BBb",
            "gas": "0x76c0",
            "gasPrice": "0x4190ab00",
            "value": "0xde0b6b3a7640000",
            "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
        }
    ]
}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

#### eth_sign

The sign method calculates an Ethereum specific signature with: sign(keccak256("\x19Ethereum Signed Message:\n" + len(message) + message))).

By adding a prefix to the message makes the calculated signature recognisable as an Ethereum specific signature. This prevents misuse where a malicious DApp can sign arbitrary data (e.g. transaction) and use the signature to impersonate the victim.

Note the address to sign with must be unlocked.

##### Parameters
account, message

1. `DATA`, 20 Bytes - address.
2. `DATA`, N Bytes - message to sign.

##### Returns

`DATA`: Signature

##### Example

```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sign","params":["0xf675187ff5b76d2430b353f6736aa051253118ee", "0xdeadbeaf"],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x49d04469c0671c7a8187c311cb84c2ae3d0ec4943563706699440d210a9fd15642381346c008bf034d629fb0b26aff0ede25678e3df9cafc937e1ff70b832e321c"
}
```

An example how to use solidity ecrecover to verify the signature calculated with `eth_sign` can be found [here](https://gist.github.com/bas-vk/d46d83da2b2b4721efb0907aecdb7ebd). The contract is deployed on the testnet Ropsten and Rinkeby.


#### eth_signTransaction

Signs a transaction that can be submitted to the network at a later time using with eth_sendRawTransaction.

##### Parameters
1. `Object` - The transaction object
    - `from`: `DATA`, 20 Bytes - (optional) The address the transaction is sent from.
    - `to`: `DATA`, 20 Bytes  - The address the transaction is directed to.
    - `gas`: `QUANTITY`  - (optional) Integer of the gas provided for the transaction execution. eth_call consumes zero gas, but this parameter may be needed by some executions.
    - `gasPrice`: `QUANTITY`  - (optional) Integer of the gasPrice used for each paid gas
    - `maxFeePerGas`: `QUANTITY` - It's just the sum of baseFeePerGas and maxPriorityFeePerGas: maxFeePerGas = baseFeePerGas + maxPriorityFeePerGas.
    - `maxPriorityFeePerGas`: `QUANTITY` - When you submit a transaction you will also provide a "tip" to the miner. This is the maxPriorityFeePerGas field. fork.
    - `value`: `QUANTITY`  - (optional) Integer of the value sent with this transaction
    - `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
    - `data`: `DATA`  - (optional) Hash of the method signature and encoded parameters
    - `input`: `DATA`  - (optional) We accept "data" and "input" for backwards-compatibility reasons. "input" is the newer name and should be preferred by clients.

##### Returns

`raw`, The signed transaction object.

Use [eth_getTransactionReceipt](#eth_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example
```json
// Request
curl -X POST --data '{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "eth_sendTransaction",
    "params": [
        {
            "from": "0xf675187ff5b76d2430b353f6736aa051253118ee",
            "to": "0xD13fE09E7A304709B1C4ed6Bd3a2D6C272357BBb",
            "gas": "0x76c0",
            "gasPrice": "0x4190ab00",
            "value": "0xde0b6b3a7640000",
            "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
        }
    ]
}'

// Result

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "raw": "0xf89680844190ab008276c094d13fe09e7a304709b1c4ed6bd3a2d6c272357bbb880de0b6b3a7640000a9d46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f0724456758201cba046644a7e36a1bb5149d72dd093ecde55d3c0999ddac6b3a19de9960931a2bf4fa010a0312b527cef2dc5ca3f26cd70d2695624c5a3c513a9f508e7eef9a4c21772",
    "tx": {
      "type": "0x0",
      "nonce": "0x0",
      "gasPrice": "0x4190ab00",
      "maxPriorityFeePerGas": null,
      "maxFeePerGas": null,
      "gas": "0x76c0",
      "value": "0xde0b6b3a7640000",
      "input": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675",
      "v": "0x1cb",
      "r": "0x46644a7e36a1bb5149d72dd093ecde55d3c0999ddac6b3a19de9960931a2bf4f",
      "s": "0x10a0312b527cef2dc5ca3f26cd70d2695624c5a3c513a9f508e7eef9a4c21772",
      "to": "0xd13fe09e7a304709b1c4ed6bd3a2d6c272357bbb",
      "hash": "0xfabcd16c9277cdc957f44554c201e71fbd4c87ca0b148c51d61d23f0363a1b45"
    }
  }
}
```

#### eth_sendRawTransaction

Creates new message call transaction or a contract creation for signed transactions.

##### Parameters

1. `DATA`, The signed transaction data.

##### Returns

`DATA`, 32 Bytes - the transaction hash, or the zero hash if the transaction is not yet available.

Use [eth_getTransactionReceipt](#eth_gettransactionreceipt) to get the contract address, after the transaction was mined, when you created a contract.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"
}
```

#### eth_newFilter

Creates a filter object, based on filter options, to notify when the state changes (logs).
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

##### A note on specifying topic filters:
Topics are order-dependent. A transaction with a log with topics [A, B] will be matched by the following topic filters:
* `[]` "anything"
* `[A]` "A in first position (and anything after)"
* `[null, B]` "anything in first position AND B in second position (and anything after)"
* `[A, B]` "A in first position AND B in second position (and anything after)"
* `[[A, B], [A, B]]` "(A OR B) in first position AND (A OR B) in second position (and anything after)"

##### Parameters

1. `Object` - The filter options:
  - `fromBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `toBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `address`: `DATA|Array`, 20 Bytes - (optional) Contract address or a list of addresses from which logs should originate.
  - `topics`: `Array of DATA`,  - (optional) Array of 32 Bytes `DATA` topics. Topics are order-dependent. Each topic can also be an array of DATA with "or" options.

##### Example Parameters
```js
params: [{
  "fromBlock": "0x1",
  "toBlock": "0x2",
  "address": "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b", null, ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b", "0x0000000000000000000000000aff3454fce5edbc8cca8697c15331677e6ebccc"]]
}]
```

##### Returns

`QUANTITY` - A filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newFilter","params":[{"topics":["0x0000000000000000000000000000000000000000000000000000000012341234"]}],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0xed3aeb3a8c77b8826c6a8544c440d7d0"
}
```

#### eth_newBlockFilter

Creates a filter in the node, to notify when a new block arrives.
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

##### Parameters
None

##### Returns

`QUANTITY` - A filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newBlockFilter","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x1270b5402002ba7baf7a4d6cbe21bf2e"
}
```



#### eth_newPendingTransactionFilter

Creates a filter in the node, to notify when new pending transactions arrive.
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

##### Parameters
None

##### Returns

`QUANTITY` - A filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newPendingTransactionFilter","params":[],"id":1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0xb392ed29025e7ccce5c6553047caee35"
}
```



#### eth_uninstallFilter

Uninstalls a filter with given id. Should always be called when watch is no longer needed.
Additonally Filters timeout when they aren't requested with [eth_getFilterChanges](#eth_getfilterchanges) for a period of time.


##### Parameters

1. `QUANTITY` - The filter id.

##### Returns

`Boolean` - `true` if the filter was successfully uninstalled, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uninstallFilter","params":["0x9c0f6318f321c61e29a88943072b8bfb"],"id":73}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": true
}
```



#### eth_getFilterChanges

Polling method for a filter, which returns an array of logs which occurred since last poll.


##### Parameters

1. `QUANTITY` - the filter id.

##### Example Parameters
```js
params: [
  "0x16" // 22
]
```

##### Returns

`Array` - Array of log objects, or an empty array if nothing has changed since last poll.

- For filters created with `eth_newBlockFilter` the return are block hashes (`DATA`, 32 Bytes), e.g. `["0x3454645634534..."]`.
- For filters created with `eth_newPendingTransactionFilter ` the return are transaction hashes (`DATA`, 32 Bytes), e.g. `["0x6345343454645..."]`.
- For filters created with `eth_newFilter` logs are objects with following params:

  - `removed`: `TAG` - `true` when the log was removed, due to a chain reorganization. `false` if its a valid log.
  - `logIndex`: `QUANTITY` - integer of the log index position in the block. `null` when its pending log.
  - `transactionIndex`: `QUANTITY` - integer of the transactions index position log was created from. `null` when its pending log.
  - `transactionHash`: `DATA`, 32 Bytes - hash of the transactions this log was created from. `null` when its pending log.
  - `blockHash`: `DATA`, 32 Bytes - hash of the block where this log was in. `null` when its pending. `null` when its pending log.
  - `blockNumber`: `QUANTITY` - the block number where this log was in. `null` when its pending. `null` when its pending log.
  - `address`: `DATA`, 20 Bytes - address from which this log originated.
  - `data`: `DATA` - contains the non-indexed arguments of the log.
  - `topics`: `Array of DATA` - Array of 0 to 4 32 Bytes `DATA` of indexed log arguments. (In *solidity*: The first topic is the *hash* of the signature of the event (e.g. `Deposit(address,bytes32,uint256)`), except you declared the event with the `anonymous` specifier.)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterChanges","params":["0x16"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "logIndex": "0x1", // 1
    "blockNumber":"0x1b4", // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
    "transactionIndex": "0x0", // 0
    "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },{
      ...
    }]
}
```



#### eth_getFilterLogs

Returns an array of all logs matching filter with given id.


##### Parameters

1. `QUANTITY` - The filter id.

##### Example Parameters
```js
params: [
  "0x16" // 22
]
```

##### Returns

See [eth_getFilterChanges](#eth_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterLogs","params":["0x16"],"id":74}'
```

Result see [eth_getFilterChanges](#eth_getfilterchanges)



#### eth_getLogs

Returns an array of all logs matching a given filter object.

##### Parameters

1. `Object` - The filter options:
  - `fromBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `toBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `address`: `DATA|Array`, 20 Bytes - (optional) Contract address or a list of addresses from which logs should originate.
  - `topics`: `Array of DATA`,  - (optional) Array of 32 Bytes `DATA` topics. Topics are order-dependent. Each topic can also be an array of DATA with "or" options.
  - `blockhash`:  `DATA`, 32 Bytes - (optional) With the addition of EIP-234 (Geth >= v1.8.13 or Parity >= v2.1.0), `blockHash` is a new filter option which restricts the logs returned to the single block with the 32-byte hash `blockHash`.  Using `blockHash` is equivalent to `fromBlock` = `toBlock` = the block number with hash `blockHash`.  If `blockHash` is present in the filter criteria, then neither `fromBlock` nor `toBlock` are allowed.

##### Example Parameters
```js
params: [{
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]
}]
```

##### Returns

See [eth_getFilterChanges](#eth_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getLogs","params":[{"topics":["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]}],"id":74}'
```

Result see [eth_getFilterChanges](#eth_getfilterchanges)


#### eth_getFilterLogs

Returns an array of all logs matching filter with given id.


##### Parameters

1. `QUANTITY` - The filter id.

##### Returns

See [eth_getFilterChanges](#eth_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterLogs","params":["0x16"],"id":74}'
```

Result see [eth_getFilterChanges](#eth_getfilterchanges)



#### eth_getLogs

Returns an array of all logs matching a given filter object.

##### Parameters

1. `Object` - The filter options:
  - `fromBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `toBlock`: `QUANTITY|TAG` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `address`: `DATA|Array`, 20 Bytes - (optional) Contract address or a list of addresses from which logs should originate.
  - `topics`: `Array of DATA`,  - (optional) Array of 32 Bytes `DATA` topics. Topics are order-dependent. Each topic can also be an array of DATA with "or" options.
  - `blockhash`:  `DATA`, 32 Bytes - (optional) With the addition of EIP-234 (Geth >= v1.8.13 or Parity >= v2.1.0), `blockHash` is a new filter option which restricts the logs returned to the single block with the 32-byte hash `blockHash`.  Using `blockHash` is equivalent to `fromBlock` = `toBlock` = the block number with hash `blockHash`.  If `blockHash` is present in the filter criteria, then neither `fromBlock` nor `toBlock` are allowed.

##### Example Parameters
```js
params: [{
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]
}]
```

##### Returns

See [eth_getFilterChanges](#eth_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getLogs","params":[{"topics":["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]}],"id":74}'
```

Result see [eth_getFilterChanges](#eth_getfilterchanges)

