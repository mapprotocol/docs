# BNBSmartChain輕客戶端

## 如何運行

BNBSmartChainLightClient 是 BNBSmartChain Light Client 在 Solidity 中作為 MapoContract 的實現。

BNB 智能鏈依賴於一個由 21 個活躍驗證者組成的系統，具有 [權益證明 (PoSA) 共識](https://github.com/bnb-chain/whitepaper/blob/master/WHITEPAPER.md#consensus-and-validator -quorum），可以支持較短的出塊時間和較低的費用。 抵押最多的驗證者候選人將成為驗證者並生產區塊。 雙簽檢測和其他罰沒邏輯保證了安全性、穩定性和鏈的最終性。

驗證器在每個紀元發生變化，每個選定的驗證者地址都被寫入塊頭的 extraData 字段中的紀元塊。 在該塊的一半數量的驗證者開始生產和驗證該塊之後。這些驗證者通過簽署包含由每個驗證者的私鑰簽名的加密簽名的塊來參與共識協議。

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

如果我們要驗證一筆交易，我們需要驗證交易所在的區塊頭，驗證一個區塊頭，我們需要驗證區塊頭的簽名。

通過跟踪驗證者的變化，輕節點可以驗證所有 bsc 交易。

## 如何驗證

#### 更新塊頭

通過不斷向輕客戶端提交紀元塊頭來跟踪驗證器的更改。提交的紀元塊必須由前一個紀元提交的驗證器之一使用私鑰簽名。 所以我們初始化一個紀元並存儲驗證者的地址可以不斷地一遍又一遍地提交下一個紀元塊。為了提高確定性，需要提交多個塊作為確認。

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

updateBlockHeader 走幾步

1.檢查第一個提交的塊是否是下一個紀元塊。

2.檢查提交的區塊數是否足夠。

3.分別驗證每個提交的區塊。

* 驗證區塊的字段

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

* 驗證區塊的簽名

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
* 驗證礦工是否重複

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

###### 驗證收據

輕客戶端在有了epoch validatorSet之後就可以驗證epoch blocks了。驗證receipt首先要驗證block transaction receipt in。驗證block和update block相似，不會再進去了。

我們知道來自大宗交易的收據形成收據 [patricia-merkle-trie](https://ethereum.org/en/developers/docs/data-structures-and-encoding/patricia-merkle-trie/)。 塊字段 receiptsRoot 是樹的根。 在我們驗證該塊之後，我們可以信任 receiptsRoot。

因此我們可以建立鏈下交易收據的證明，提交給輕客戶端以證明交易收據。

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

## 證明

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
