# Tx-Verify-Contract

## Contract Address

tx verify contract is deployed at address:

```
```

## Contract JSON ABI

```json
[
  {
    "inputs": [
      {
        "internalType": "address",
        "name": "router",
        "type": "address"
      },
      {
        "internalType": "address",
        "name": "coin",
        "type": "address"
      },
      {
        "internalType": "uint256",
        "name": "srcChain",
        "type": "uint256"
      },
      {
        "internalType": "uint256",
        "name": "dstChain",
        "type": "uint256"
      },
      {
        "internalType": "bytes",
        "name": "txProve",
        "type": "bytes"
      }
    ],
    "name": "txVerify",
    "outputs": [
      {
        "internalType": "bool",
        "name": "success",
        "type": "bool"
      },
      {
        "internalType": "string",
        "name": "message",
        "type": "string"
      }
    ],
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

## Interact with contract interface

### txVerify

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| router   | Address      | address of the contract that generated the cross-chain transaction event |
| coin     | Address      | the address of the token contract |
| srcChain | *big.Int     | source chain identification |
| dstChain | *big.Int     | destination chain identification|
| rlpTxProve  | []byte       | cross chain transaction prove information, RLP encode of [txProve](Tx-Verify#MAP) |

#### output parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| success | bool          | if the verification is successful, is true |
| message | string        | if the verification is successful, is empty |

### example

```
package main

import (
	"math/big"
	"strings"

	"github.com/ethereum/go-ethereum/accounts/abi"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/light"
	"github.com/ethereum/go-ethereum/rlp"
	
	"github.com/mapprotocol/atlas/core/types"
)

type TxParams struct {
	From  []byte
	To    []byte
	Value *big.Int
}

type TxProve struct {
    Header      []byte
	Tx          *TxParams
	Receipt     []byte
	Prove       light.NodeList
}

func example() {
	var (
	    coin     = common.Address{}.Bytes()
	    router   = common.Address{}.Bytes()
	    srcChain = big.NewInt(1)
	    dstChain = big.NewInt(211)
	)

    encodedHeader, err := rlp.EncodeToBytes(types.Header{})
	if err != nil {
		panic(err)
	}
	encodedReceipt, err := rlp.EncodeToBytes(types.Receipt{})
	if err != nil {
		panic(err)
	}
	
	txProve := TxProve{
		Header: encodedHeader,
		Tx: &TxParams{
			From:  commom.Address{}.Bytes(),
			To:    commom.Address{}.Bytes(),
			Value: big.NewInt(1),
		},
		Receipt: encodedReceipts,
		Prove:   proof.NodeList(),
	}

	encodedTxProve, err := rlp.EncodeToBytes(txProve)
	if err != nil {
		panic(err)
	}

	ABITxVerify, _ := abi.JSON(strings.NewReader(""))
	input, err := ABITxVerify.Pack("txVerify", router, coin, srcChain, dstChain, encodedTxProve)
	if err != nil {
		panic(err)
	}
	
	// Send Transaction ...
	
	return
}
```

