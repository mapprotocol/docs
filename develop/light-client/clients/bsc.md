# BNBSmartChain Light Client

## How it works

BNBSmartChainLightClient is an implementation of the BNBSmartChain Light Client in Solidity as an MapoContract.

BNB Smart Chain relies on a system of 21 active validators with [Proof of Staked Authority (PoSA) consensus](https://github.com/bnb-chain/whitepaper/blob/master/WHITEPAPER.md#consensus-and-validator-quorum) that can support short block time and lower fees. The most bonded validator candidates of staking will become validators and produce blocks. The double-sign detection and other slashing logic guarantee security, stability, and chain finality.

The validators changes every epoch,each selected validator address is written to the epoch block in the extraData field of the block header. after half the number of validators of the block begins production and validation of the block.These validators participate in the consensus protocol by signing blocks that contain cryptographic signatures signed by each validator's private key.

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
    }
```

If we want to validate a transaction, we need to validate the block header that the transaction is in,to validate a block header and we need to validate the signature of the block header.

by tracking validators changes light node can verify all bsc transations.

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
        require(_lastSyncedBlock > 0,"light node not initialize");
        _lastSyncedBlock += EPOCH_NUM;

        require(
            _blockHeaders[0].number == _lastSyncedBlock,
            "invalid start block"
        );
        // min is number of validators half + 1
        uint256 min = _getValidatorNum( validators[_lastSyncedBlock - EPOCH_NUM]) / 2 + 1;

        require(_blockHeaders.length >= min, "not enough");

        require(_verifyBlockHeaders(_blockHeaders, min), "blocks verify fail");

        validators[_lastSyncedBlock] = Verify.getValidators(
            _blockHeaders[0].extraData
        );

        _removeExcessEpochValidators();

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
        uint256 _parentGasLimit,
        uint256 _minEpochBlockExtraDataLen
    ) internal pure returns (bool) {
        if (_header.extraData.length < (EXTRA_VANITY + EXTRASEAL)) {
            return false;
        }
        //Epoch block
        if (_header.number % EPOCH_NUM == 0) {
            if (_header.extraData.length < _minEpochBlockExtraDataLen) {
                return false;
            }
        }

        if (_header.difficulty != 2 && _header.difficulty != 1) {
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
        //2**63 - 1 maxGasLimit
        if (
            _header.gasLimit > 2 ** 63 - 1 || _header.gasLimit < _header.gasUsed
        ) {
            return false;
        }

        uint256 diff = _parentGasLimit > _header.gasLimit
            ? _parentGasLimit - _header.gasLimit
            : _header.gasLimit - _parentGasLimit;
        //5000 minGasLimit
        if (diff >= _parentGasLimit / 256 || _header.gasLimit < MIN_GAS_LIMIT) {
            return false;
        }

        return true;
    }
```

* verify the signature of the block

  ```solidity
      function verifyHeaderSignature(
          BlockHeader memory _header,
          uint256 _chainId
      ) internal pure returns (bool) {
          (bytes memory signature, bytes memory extraData) = splitExtra(
              _header.extraData
          );

          bytes32 hash = keccak256(encodeSigHeader(_header, extraData, _chainId));

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

          return signer == _header.miner;
      }

  ```
* check if miner is in the corresponding validatorSet.

  ```solidity
        require(
                  Verify.containValidator(
                      vals,
                      _blockHeaders[i].miner,
                      _blockHeaders[i].number % (_getValidatorNum(vals))
                  ),
                  "invalid miner"
              );
  ```
* Verify that miner is duplicate

  ```solidity
              if (i > 0) {
                  require(
                      !_isRepeat(miners, _blockHeaders[i].miner, i),
                      "miner repeat"
                  );
              }
  ```

4.store validatorSet.

```solidity
      validators[_lastSyncedBlock] = Verify.getValidators(
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

        if (success)
            logs = bytesReceipt.toRlpItem().toList()[3].toRlpBytes(); // list length must be 4
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
