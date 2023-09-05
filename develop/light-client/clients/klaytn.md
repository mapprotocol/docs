# Klaytn Light Client

## How it works

KlaytnLightClient is the implementation of a Klaytn light client on the Mapo Relay Chain.

Klaytn achieves fast finality by adopting and improving Istanbul BFT. Because validation and consensus are done for each block there is no fork and the block's finality is guaranteed instantly as soon as the consensus is made.

And also the issue of increasing communication volume in the BFT algorithm is solved by utilizing randomly selected [Committee](https://docs.klaytn.foundation/content/klaytn/design/consensus-mechanism). CNs collectively form a `Council` and on each block generation, part of them are selected as a member of `Committee` using a VRF (Verifiable Random Function).

In the Klaytn network, all validators and `CommitSeal` are included in the `extraData` field of the block header,[the length of CommitSeal should be larger than number of faulty node + 1](https://github.com/klaytn/klaytn/blob/bac53451b1a89391ba8598a4a32129d2078402df/consensus/istanbul/backend/engine.go#L359),These validators participate in the consensus protocol by signing blocks that contain cryptographic signatures signed by each validator's private key.

```
    struct BlockHeader {
        bytes parentHash;
        address reward;
        bytes stateRoot;
        bytes transactionsRoot;
        bytes receiptsRoot;
        bytes logsBloom;
        uint256 blockScore;
        uint256 number;
        uint256 gasUsed;
        uint256 timestamp;
        uint256 timestampFoS;
        bytes extraData;
        bytes governanceData;
        bytes voteData;
        uint256 baseFee;
    }
```

If we want to validate a transaction, we need to validate the block header that the transaction is in,to validate a block header and we need to validate the signature of the block header.

by tracking validators changes light node can verify all klaytn transations.

## How to verify

#### updateBlockHeader

Ensure that the light client validators are updated by continuously providing block headers. In the Klaytn chain, validators can join or exit at any time. Assuming an epoch consists of 3600 blocks, for each epoch, block headers submitted must be verified by validators from the previous epoch using their private keys.

Similarly, when changes occur to the validators, the `voteData` field in the block header will be updated accordingly. The light client will not strictly adhere to epochs but will promptly submit records of changed block headers and updated validators to maintain the authenticity of the verification process.

```
    function updateBlockHeader(bytes memory _blockHeaders)
    external
    override
    {
        IKlaytn.BlockHeader[] memory _headers = abi.decode(
            _blockHeaders, (IKlaytn.BlockHeader[]));

        require(_headers[0].number > lastEpochHeight, "Height error");

        if (_headers[0].number % CHANGE_VALIDATORS_SIZE > 0) {
            _updateBlockHeaderChange(_headers);
        } else {
            for (uint256 i = 0; i < _headers.length; i++) {
                require(_headers[i].number == lastEpochHeight + CHANGE_VALIDATORS_SIZE, "Height epoch error");
                IKlaytn.BlockHeader memory bh = _headers[i];
                (bool success, IKlaytn.ExtraData memory data) = checkBlockHeader(bh, false);
                require(success, "Header verify fail");

                uint256 validatorIdx = _getValidatorIndex(bh.number);
                Validator memory tempValidators = validators[validatorIdx];

                _cleanValidator(tempValidators.headerHeight);

                Validator memory v = Validator({
                validators : data.validators,
                headerHeight : bh.number
                });
                validators[validatorIdx] = v;
                lastEpochHeight = bh.number;

                if (lastEpochHeight - firstEpochHeight >= CHANGE_VALIDATORS_SIZE * MAX_EPOCH_SIZE) {
                    firstEpochHeight = firstEpochHeight + CHANGE_VALIDATORS_SIZE;
                }
                emit UpdateBlockHeader(msg.sender, lastEpochHeight);
            }
        }
    }
```

updateBlockHeader take a few steps

1.check that the first committed block is the next epoch block.

2. Verify the submitted block header.

- validate the field of the block

```
    function checkBlockHeader(BlockHeader memory _header,bool _tag)
    internal
    view
    returns (bool, ExtraData memory)
    {

        bool success = verifyTool.checkHeaderParam(_header);

        require(success, "header param error");

        (bytes memory extHead, ExtraData memory ext) = verifyTool.decodeHeaderExtraData(_header.extraData);
        (bytes memory extraNoSeal, bytes memory seal) = verifyTool.getRemoveSealExtraData(ext, extHead, false);
        (bytes memory extra,) = verifyTool.getRemoveSealExtraData(ext, extHead, true);
        (bytes32 hash,bytes32 signerHash) = verifyTool.getBlockNewHash(_header, extra,extraNoSeal);

        address signer = verifyTool.recoverSigner(seal, keccak256(abi.encodePacked(signerHash)));

        uint num = _header.number;

        if(!_tag){
            num = _header.number - CHANGE_VALIDATORS_SIZE;
        }

        Validator memory v = _getCanVerifyValidator(num,_tag);

        require(v.headerHeight > 0, "validator load fail");

        require(v.headerHeight + CHANGE_VALIDATORS_SIZE >= _header.number, "check block height error");

        success = _checkCommittedAddress(v.validators, signer);

        require(success, "signer fail");

        bytes memory committedMsg = abi.encodePacked(hash, MSG_COMMIT);

        return (_checkCommitSeal(v, committedMsg, ext.committedSeal), ext);
    }
```

- verify the signature of the block

  ```
      
  function recoverSigner(
          bytes memory _seal,
          bytes32 _hash)
      external
      pure
      returns (address)
      {
          (bytes32 r, bytes32 s, uint8 v) = _splitSignature(_seal);
          if (v <= 1) {
              v = v + 27;
          }
          return ECDSA.recover(_hash, v, r, s);
      }
      
  function _splitSignature(bytes memory _sig)
      internal
      pure
      returns (bytes32 r, bytes32 s, uint8 v)
      {
          require(_sig.length == 65, "invalid signature length");
          assembly {
              r := mload(add(_sig, 32))
              s := mload(add(_sig, 64))
              v := byte(0, mload(add(_sig, 96)))
          }
      }
  ```

- Check if the `CommitSeal` is a valid signature and if the quantity requirements are met.

  ```
      function _checkCommitSeal(
          Validator memory _v,
          bytes memory _committedMsg,
          bytes[] memory _committedSeal)
      internal
      view
      returns (bool)
      {
          bytes32 msgHash = keccak256(_committedMsg);
          address[] memory miners = new address[](_v.validators.length);

          uint checkedCommittee = 0;
          for (uint i = 0; i < _committedSeal.length; i++) {
              address committee = verifyTool.recoverSigner(_committedSeal[i], msgHash);
              if (_checkCommittedAddress(_v.validators,committee) && !(verifyTool.isRepeat(miners,committee,i))) {
                  checkedCommittee++;
              }
              miners[i] = committee;
          }
          return checkedCommittee > (_getFaultyNodeNumber(_v.validators.length)) * 2;
      }
  ```

- Verify that miner is duplicate

  ```
      function isRepeat(
          address[] memory _miners,
          address _miner,
          uint256 _limit)
      external
      pure
      returns (bool)
      {
          for (uint256 i = 0; i < _limit; i++) {
              if (_miners[i] == _miner) {
                  return true;
              }
          }

          return false;
      }
  ```

###### verify receipt

Due to the unique characteristics of the Klaytn chain, it upgraded to the DeriveShaOriginal mode after block number 128822400. This mode is currently the prevalent transaction receipt proof verification mode.

The light client can verify the epoch blocks after it has the epoch validatorSet.to verify the receipt should first veriy the block transation receipt in.verify the block is similar to the update block,won't go into it again.

we know that receipts from block transactions form a receipt [patricia-merkle-trie](https://ethereum.org/en/developers/docs/data-structures-and-encoding/patricia-merkle-trie/). the block field receiptsRoot is the root of the tree. after we verify the block we can trust the receiptsRoot.

so we can build proof of the transation receipt off chain submit to light client to proof transaton receipts.

```
    function verifyProofData(bytes memory _receiptProof)
    external
    view
    override
    returns (bool success,
        string memory message,
        bytes memory logs)
    {
        IKlaytn.ReceiptProof memory receiptProof = abi.decode(_receiptProof, (IKlaytn.ReceiptProof));

        if (receiptProof.deriveSha == IKlaytn.DeriveShaOriginal.DeriveShaConcat) {
            IKlaytn.ReceiptProofConcat memory proof = abi.decode(receiptProof.proof, (IKlaytn.ReceiptProofConcat));
            IKlaytn.BlockHeader memory header = proof.header;
            (success, ) = checkBlockHeader(header,true);
            if (!success) {
                message = "DeriveShaConcat header verify failed";
                return(success,message,logs);
            }
            success = verifyTool.checkReceiptsConcat(proof.receipts, (bytes32)(header.receiptsRoot));
            if (success) {
                bytes memory bytesReceipt = proof.receipts[proof.logIndex];

                logs = bytesReceipt.toRlpItem().toList()[RLP_INDEX].toRlpBytes();

                message = "DeriveShaConcat mpt verify success";
                return(success, message, logs);
            }else{
                message = "DeriveShaConcat mpt verify failed";
                return(success, message, logs);
            }
        } else if (receiptProof.deriveSha == IKlaytn.DeriveShaOriginal.DeriveShaOriginal) {
            IKlaytn.ReceiptProofOriginal memory proof = abi.decode(receiptProof.proof, (IKlaytn.ReceiptProofOriginal));
            (success, ) = checkBlockHeader(proof.header,true);
            if(!success){
                message = "DeriveShaOriginal header verify failed";
                return(success, message, logs);
            }

            success = mptVerifier.verifyTrieProof(bytes32(proof.header.receiptsRoot), proof.keyIndex, proof.proof, proof.txReceipt);

            if (success) {
                message = "DeriveShaOriginal mpt verify success";

                logs = proof.txReceipt.toRlpItem().toList()[RLP_INDEX].toRlpBytes();

                return(success, message, logs);
            } else {
                message = "DeriveShaOriginal mpt verify failed";
                return(false, message, logs);
            }
        } else {
            message = "mpt verify failed";
            return(false, message, logs);
        }
    }
```

## Proof Data

```
    struct ReceiptProof {
        bytes proof;
        DeriveShaOriginal deriveSha;
    }

    struct ReceiptProofOriginal {
        BlockHeader header;
        bytes[] proof;
        bytes txReceipt;
        bytes keyIndex;
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