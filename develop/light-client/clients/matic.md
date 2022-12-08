# Polygon Light Client

## How it works

PolygonLightClient is an implementation of the polygon Light Client in Solidity as an MapoContract.

Polygon is a Proof-of-stake system. Anyone can stake their Matic token on Ethereum smart-contract, "staking contract", and become a validator for the system.Once validators are active on Heimdall they get selected as producers through `bor` module.

[Bor](https://wiki.polygon.technology/docs/pos/bor/consensus/) consensus is inspired by Clique consensus: [https://eips.ethereum.org/EIPS/eip-225](https://eips.ethereum.org/EIPS/eip-225). Clique works with multiple pre-defined producers. All producers vote on new producers using Clique APIs. They take turns creating blocks.

Bor fetches new producers through span and sprint management mechanism.

The validators changes every epoch,each selected validator address is written to the epoch block in the extraData field of the block header. next block begins production and validation of the block.These validators participate in the consensus protocol by signing blocks that contain cryptographic signatures signed by each validator's private key.

```solidity
    struct BlockHeader {
        bytes parentHash;
        bytes sha3Uncles;
        address miner;
        bytes stateRoot;
        bytes transactionsRoot;
        bytes receiptsRoot;
        bytes logsBloom;
        uint256 difficulty;
        uint256 number;
        uint256 gasLimit;
        uint256 gasUsed;
        uint256 timestamp;
        bytes extraData;
        bytes mixHash;
        bytes nonce;
        uint256 baseFeePerGas;
    }
```

If we want to validate a transaction, we need to validate the block header that the transaction is in,to validate a block header and we need to validate the signature of the block header.

by tracking validators changes light node can verify all  transations.

## How to verify

#### updateBlockHeader

keep track of the validator's changes by continuously submitting epoch block headers to light client.The submitted epoch block must be signed with the private key by one of the validator's submitted in the previous epoch. so we initialize an epoch and  store the validator's address can keep committing the next epoch block over and over again.to improve certainty, multiple blocks need to be submitted as confirms.

```solidity
 function updateBlockHeader(
        bytes memory _blockHeadersBytes
    ) external override whenNotPaused {
        Verify.BlockHeader[] memory _blockHeaders = abi.decode(
            _blockHeadersBytes,
            (Verify.BlockHeader[])
        );

        require(confirms > 0, " not initialize");

        require(_blockHeaders.length == confirms, "not enough");

        _lastSyncedBlock += EPOCH_NUM;

        require(
            _blockHeaders[0].number == _lastSyncedBlock,
            "invalid syncing block"
        );
        // index 0 header verify by pre validators others by index 0 getValidators
        validators[(_lastSyncedBlock + 1) / EPOCH_NUM] = Verify.getValidators(
            _blockHeaders[0].extraData
        );
        require(_verifyBlockHeaders(_blockHeaders), "blocks verify fail");

        emit UpdateBlockHeader(tx.origin, _blockHeaders[0].number);
    }
```

updateBlockHeader take a few steps

1.check that the first committed block is the next epoch block.

2.check that the number of blocks submitted is sufficient.

3.verify each submitted block separately.

* validate the field of the block

```solidity
  function validateHeader(
        BlockHeader memory _header,
        uint256 _minEpochBlockExtraDataLen,
        BlockHeader memory _parent
    ) internal pure returns (bool) {
        if (_header.extraData.length < (EXTRA_VANITY + EXTRASEAL)) {
            return false;
        }
        //Epoch block
        if ((_header.number + 1) % EPOCH_NUM == 0) {
            if (_header.extraData.length < _minEpochBlockExtraDataLen) {
                return false;
            }
        }

        if (_header.difficulty > 200) {
            return false;
        }

        if (_header.parentHash.length != 32) {
            return false;
        }

        if (_header.miner != address(0)) {
            return false;
        }

        if (
            _header.sha3Uncles.length != 32 ||
            bytes32(_header.sha3Uncles) != SHA3_UNCLES
        ) {
            return false;
        }

        if (_header.nonce.length != 8 || bytes8(_header.nonce) != NONCE) {
            return false;
        }

        if (
            _header.mixHash.length != 32 || bytes32(_header.mixHash) != MIX_HASH
        ) {
            return false;
        }
        //2**63 - 1 maxGasLimit minGasLimit 5000
        if (
            _header.gasLimit > 2 ** 63 - 1 ||
            _header.gasLimit < MIN_GAS_LIMIT ||
            _header.gasLimit < _header.gasUsed
        ) {
            return false;
        }

        if (_header.number != _parent.number) {
        
            if(_header.timestamp <= _parent.timestamp) {
                return false;
            }
            uint256 diff = _parent.gasLimit > _header.gasLimit
                ? _parent.gasLimit - _header.gasLimit
                : _header.gasLimit - _parent.gasLimit;
      
            if (diff >= _parent.gasLimit / 1024) {
                return false;
            }

            uint256 expectedBaseFee = calcBaseFee(
                _parent.gasUsed,
                _parent.gasLimit,
                _parent.baseFeePerGas
            );

            if (_header.baseFeePerGas != expectedBaseFee) {
                return false;
            }
        }

        return true;
    }
```

* verify the signature of the block

  ```solidity
      function recoverSigner(
          BlockHeader memory _header
      ) internal pure returns (address) {
          (bytes memory signature, bytes memory extraData) = splitExtra(
              _header.extraData
          );

          bytes32 hash = keccak256(encodeSigHeader(_header, extraData));

          bytes32 r;
          bytes32 s;
          uint8 v;
          // ecrecover takes the signature parameters, and the only way to get them
          // currently is to use assembly.
          assembly {
              r := mload(add(signature, 0x20))
              s := mload(add(signature, 0x40))
              v := byte(0, mload(add(signature, 0x60)))
          }
          if (v <= 1) {
              v = v + 27;
          }

          address signer = ecrecover(hash, v, r, s);

          return signer;
      }
  ```
* check if siger is in the corresponding validatorSet.

  ```solidity
              address signer = Verify.recoverSigner(_blockHeaders[i]);
              require(
                  Verify.containValidator(
                      validators[_blockHeaders[i].number / EPOCH_NUM],
                      signer
                  ),
                  "invalid block header singer"
              );
  ```

4.store validatorSet.

```solidity
        validators[(_lastSyncedBlock + 1) / EPOCH_NUM] = Verify.getValidators(
            _blockHeaders[0].extraData
        );
```

###### verify receipt

The light client can verify the epoch blocks after it has the epoch validatorSet.to verify the receipt should first veriy the block transation receipt in.verify the block is similar to the update block,won't go into it again.

we know that receipts from block transactions form a receipt [patricia-merkle-trie](https://ethereum.org/en/developers/docs/data-structures-and-encoding/patricia-merkle-trie/). the block field  receiptsRoot is the root of the tree. after we verify the block we can trust the receiptsRoot.

so we can build proof of the transation receipt off chain submit to light client to proof transaton receipts.

```solidity
    function validateProof(
        bytes32 _receiptsRoot,
        ReceiptProof memory _receipt,
        address _mptVerify
    ) internal pure returns (bool success, bytes memory logs) {
        bytes memory bytesReceipt = encodeReceipt(_receipt.txReceipt);
        bytes memory expectedValue = bytesReceipt;
        if (_receipt.txReceipt.receiptType > 0) {
            expectedValue = abi.encodePacked(
                bytes1(uint8(_receipt.txReceipt.receiptType)),
                bytesReceipt
            );
        }

        success = IMPTVerify(_mptVerify).verifyTrieProof(
            _receiptsRoot,
            _receipt.keyIndex,
            _receipt.proof,
            expectedValue
        );

        if (success) logs = bytesReceipt.toRlpItem().toList()[3].toRlpBytes(); // list length must be 4
    }
```

## Proof

```solidity
    struct ProofData {
        Verify.BlockHeader[] headers;   // proof block headers
        Verify.ReceiptProof receiptProof; 
    }

    struct ReceiptProof {
        TxReceipt txReceipt;
        bytes keyIndex;
        bytes[] proof;
    } 

    struct TxReceipt {
        uint256 receiptType;
        bytes postStateOrStatus;
        uint256 cumulativeGasUsed;
        bytes bloom;
        TxLog[] logs;
    }

    struct TxLog {
        address addr;
        bytes[] topics;
        bytes data;
    }
```
