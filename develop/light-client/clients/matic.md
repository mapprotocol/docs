# 多邊形輕客戶端

## 如何運行

PolygonLightClient 是 Solidity 中作為 MapoContract 在Polygon Light Client 的實現。

Polygon 是一個權益證明系統。 任何人都可以將他們的 Matic 代幣質押在以太坊智能合約“質押合約”上，並成為系統的驗證者。一旦驗證者在 Heimdall 上活躍，他們就會通過“bor”模塊被選為生產者。

[Bor](https://wiki.polygon.technology/docs/pos/bor/consensus/) 共識的靈感來自 Clique 共識：[https://eips.ethereum.org/EIPS/eip-225](https: //eips.ethereum.org/EIPS/eip-225）。 Clique 與多個預定義的生產者合作。 所有生產者都使用 Clique API 對新生產者進行投票。 他們輪流創建塊。

Bor 通過 span 和 sprint 管理機制獲取新的生產者。

驗證器在每個紀元發生變化，每個選定的驗證器地址都被寫入塊頭的 extraData 字段中的紀元塊。 下一個塊開始生產和驗證塊。這些驗證器通過簽署包含由每個驗證器的私鑰簽名的加密簽名的塊來參與共識協議。

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

如果我們要驗證一筆交易，我們需要驗證交易所在的區塊頭，驗證一個區塊頭，我們需要驗證區塊頭的簽名。

通過跟踪驗證器的變化，輕節點可以驗證所有交易。

## 如何驗證

#### 更新區塊頭

通過不斷向輕客戶端提交紀元塊頭來跟踪驗證器的更改。提交的紀元塊必須由前一個紀元提交的驗證器之一使用私鑰簽名。 所以我們初始化一個紀元並存儲驗證者的地址可以不斷地一遍又一遍地提交下一個紀元塊。為了提高確定性，需要提交多個塊作為確認。

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

更新 BlockHeader 分以下幾步

1.檢查第一個提交的塊是否是下一個紀元塊。

2.檢查提交的塊數是否足夠。

3.分別驗證每個提交的塊。

* 驗證塊的字段

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

* 驗證區塊的簽名

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
* 檢查siger是否在對應的validatorSet中。

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

        if (success) logs = bytesReceipt.toRlpItem().toList()[3].toRlpBytes(); // list length must be 4
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
