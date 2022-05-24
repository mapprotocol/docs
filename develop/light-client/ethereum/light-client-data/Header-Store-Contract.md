# Header-Store-Contract

## Contract Address

header store contract is deployed at address:

```
0x000068656164657273746F726541646472657373
```

## Contract json ABI

```json
[
  {
    "inputs": [
      {
        "internalType": "uint256",
        "name": "chainID",
        "type": "uint256"
      }
    ],
    "name": "currentNumberAndHash",
    "outputs": [
      {
        "internalType": "uint256",
        "name": "number",
        "type": "uint256"
      },
      {
        "internalType": "bytes",
        "name": "hash",
        "type": "bytes"
      }
    ],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [],
    "name": "getRelayer",
    "outputs": [
      {
        "internalType": "address",
        "name": "relayer",
        "type": "address"
      }
    ],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [
      {
        "internalType": "uint256",
        "name": "from",
        "type": "uint256"
      },
      {
        "internalType": "uint256",
        "name": "td",
        "type": "uint256"
      },
      {
        "internalType": "bytes",
        "name": "header",
        "type": "bytes"
      }
    ],
    "name": "reset",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [
      {
        "internalType": "uint256",
        "name": "from",
        "type": "uint256"
      },
      {
        "internalType": "uint256",
        "name": "to",
        "type": "uint256"
      },
      {
        "internalType": "bytes",
        "name": "headers",
        "type": "bytes"
      }
    ],
    "name": "save",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [
      {
        "internalType": "address",
        "name": "relayer",
        "type": "address"
      }
    ],
    "name": "setRelayer",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

## Interact with contract interface

### save

validate and save the block header synchronized by the relayer

#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| from     | number | source chain identification |
| to       | number | destination chain identification |
| headers  | []byte | a batch of block header rlp encoded data, example: rlp.EncodeToBytes([]Header{}) |

### currentHeaderNumber

get the current synchronized part height of the corresponding chain

#### parameters

| parameter | type   | comment |
| --------- | ------ | ------- |
| chainID   | number | chain identification |

### setRelayer

set the address of the relayer contract. This contract is only called by admin

#### parameters

| parameter | type   | comment |
| --------- | ------ | ------- |
| relayer   | address | the address of the relayer contract |

### getRelayer

get the address of the relayer contract

#### response

| parameter | type   | comment |
| --------- | ------ | ------- |
| relayer   | address | the address of the relayer contract |

### reset

clear the header data stored in the header store contract, and use the specified header as the starting block header 
used by the header store contract. This contract is only called by admin

#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| from     | number | source chain identification |
| td       | number | the total difficulty of the block header you want to set |
| header   | []byte | a block header rlp encoded data, example: rlp.EncodeToBytes(Header{}) |
