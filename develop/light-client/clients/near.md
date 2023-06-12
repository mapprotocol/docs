# 近協議輕客戶端

## 如何運行的

NearProtocolLightClient 是 [NEAR 輕客戶端](https://nomicon.io/ChainSpec/LightClient) 在 Solidity 中作為 Mapo 合約的實現。 輕客戶端的狀態定義為：

1. 當前頭部的`BlockHeaderInnerLiteView`（包含`height`, `epoch_id`, `next_epoch_id`, `prev_state_root`, `outcome_root`, `timestamp`，為下一個epoch設置的區塊生產者的hash `next_bp_hash` , 以及所有區塊哈希的 merkle 根 `block_merkle_root`);
2. 當前和下一個時期的區塊生產者集合。

`epoch_id` 指的是當前已知頭部的區塊所屬的紀元，而 `next_epoch_id` 是接下來的紀元。

輕客戶端通過[此處](https://nomicon.io/ChainSpec/LightClient#rpc-end-points) 描述的特定 RPC 端點定期獲取 LightClientBlockView 的實例來運行。

輕客戶端不需要接收所有塊的 `LightClientBlockView`。 擁有塊“B”的“LightClientBlockView”足以驗證“B”祖先（包括“B”本身）中任何塊中關於狀態或結果的任何陳述。 特別是，擁有頭部的 LightClientBlockView 足以在本地驗證規範鏈上任何區塊中關於狀態或結果的任何陳述。

然而，為了驗證特定“LightClientBlockView”的有效性，輕客戶端必須在前一個紀元中驗證至少一個區塊的“LightClientBlockView”，因此要同步到頭部，輕客戶端必須獲取並驗證一個“ LightClientBlockView` 每個時代過去了。

## 如何驗證

#### 更新塊頭

```solidity
struct LightClientBlock {
        bytes32 prev_block_hash;
        bytes32 next_block_inner_hash;
        BlockHeaderInnerLite inner_lite;
        bytes32 inner_rest_hash;
        OptionalBlockProducers next_bps;
        OptionalSignature[] approvals_after_next;
        bytes32 hash;   
        bytes32 next_hash;
}

struct BlockHeaderInnerLite {
        uint64 height; // Height of this block since the genesis block (height 0).
        bytes32 epoch_id; // Epoch start hash of this block's epoch. Used for retrieving validator information
        bytes32 next_epoch_id;
        bytes32 prev_state_root; // Root hash of the state at the previous block.
        bytes32 outcome_root; // Root of the outcomes of transactions and receipts.
        uint64 timestamp; // Timestamp at which the block was built.
        bytes32 next_bp_hash; // Hash of the next epoch block producers set
        bytes32 block_merkle_root;
        bytes32 hash; // Additional computable element
}

struct PublicKey {
        bytes32 k;
}

struct Signature {
        bytes32 r;
        bytes32 s;
}

struct BlockProducer {
        PublicKey publicKey;
        uint128 stake;
        // Flag indicating if this validator proposed to be a chunk-only producer (i.e. cannot become a block producer).
        bool isChunkOnly;
}

struct OptionalBlockProducers {
        bool some;
        BlockProducer[] blockProducers;
        bytes32 hash; // Additional computable element
}

struct OptionalSignature {
        bool some;
        Signature signature;
}



```

`prev_block_hash`、`next_block_inner_hash` 和 `inner_rest_hash` 字段用於重建當前和下一個塊的哈希值，以及將要簽署的批准，按照以下方式（其中 `block_view` 是 `LightClientBlockView` 的實例 ):

```solidity
hash = sha256(
            abi.encodePacked(sha256(abi.encodePacked(sha256(borsh(inner_lite), inner_rest_hash)), prev_block_hash)
        );

next_hash = sha256(abi.encodePacked(next_block_inner_hash,hash));

approval_message =  abi.encodePacked(
                uint8(0),
                nextHash,
                Utils.swapBytes8(blockHeight + 2),
                bytes23(0)
            )
```
輕客戶端使用來自 LightClientBlockView 的信息更新它的頭部，當且僅當：

1.區塊高度高於當前區塊頭高度；
2. 區塊的epoch等於當前頭部已知的`next_epoch_id`；
3. 如果區塊的epoch等於頭部的`next_epoch_id`，則`next_bps`不為`None`；
4. `approvals_after_next` 包含來自相應時期的區塊生產者的 `approval_message` 上的有效簽名（見下一節）；
5. `approvals_after_next` 中的簽名對應於總股份的 2/3 以上（見下一節）。
6.如果`next_bps`不為none，則`sha256(borsh(next_bps))`對應`inner_lite`中的`next_bp_hash`。

   ```solidity
     // 1
     require(nearBlock.inner_lite.height > curHeight,"New block must have higher height");
     //2
     require(nearBlock.inner_lite.epoch_id == nextEpochId,"initialized or unknown epoch");
     //3 
     require(nearBlock.next_bps.some,"Next next_bps should not be None");
     //4
     if (approval.some) {
                   bool check = Ed25519Verify.checkBlockProducerSignatureInHead(
                       thisEpoch.keys[i],
                       approval.signature.r,
                       approval.signature.s,
                       nearBlock.next_hash,
                       nearBlock.inner_lite.height
                   );
                   if (!check) {
                       return (false, "Invalid Signature");
                   }
       }
      //5 
       uint256 votedFor = 0;
           for ((uint256 i, uint256 cnt) = (0, thisEpoch.numBPs); i != cnt; ++i) {
               bytes32 stakes = thisEpoch.packedStakes[i >> 1];
               if (nearBlock.approvals_after_next[i].some) {
                   votedFor += uint128(bytes16(stakes));
               }
               if (++i == cnt) {
                   break;
               }
               if (nearBlock.approvals_after_next[i].some) {
                   votedFor += uint128(uint256(stakes));
               }
           }

           if (votedFor <= thisEpoch.stakeThreshold) {
               return (false, "Too few approvals");
           }
   //6 
     require(nearBlock.next_bps.hash == nearBlock.inner_lite.next_bp_hash,"Hash of block producers does not match");
   ```

   為了簡化協議，我們要求下一個塊和下一個塊之後的塊都與 LightClientBlockView 對應的塊處於同一紀元。 保證每個紀元至少有一個最終區塊，在它之上構建的下兩個區塊在同一紀元中。

    通過在“LightClientBlockView”被驗證時構建，為其紀元設置的區塊生產者是已知的。 具體來說，當處理前一個epoch的第一個輕客戶端塊視圖時，由於上面的（3）'next_bps'不是'None'，並且由於上面的（6）它對應於塊中的'next_bp_hash' 標頭。

    前一個 epoch 的 `next_bps` 的所有 stake 之和就是上面（5）中提到的 `total_stake`。

    `LightClientBlockView::approvals_after_next` 中的簽名是 `approval_message` 上的簽名。 `approvals_after_next` 中的第 `i` 個簽名（如果存在）必鬚根據前一個紀元的 `next_bps` 中的第 `i` 個公鑰進行驗證。 `approvals_after_next` 可以包含比前一個紀元中的 `next_bps` 更少的元素。

    `approvals_after_next` 還可以包含比前一個紀元中的 `next_bps` 長度更多的簽名。 這是因為，根據 [共識規範](https://nomicon.io/ChainSpec/Consensus)，每個紀元中的最後一個區塊都包含來自當前紀元和下一個紀元的區塊生產者的簽名 . 輕客戶端實現可以安全地忽略尾隨簽名。
#### 驗證證明數據

為了驗證交易或收據是否發生在鏈上，輕客戶端可以通過 rpc 提供 receipt_id 和輕客戶端頭部的塊哈希來請求證明。 rpc 將返回以下結構

```solidity

struct FullOutcomeProof {
        ExecutionOutcomeWithIdAndProof outcome_proof;
        MerklePath outcome_root_proof; 
        BlockHeaderLight block_header_lite;
        MerklePath block_proof;
}
struct ExecutionOutcomeWithIdAndProof {
        MerklePath proof;
        bytes32 block_hash;
        ExecutionOutcomeWithId outcome_with_id;
}
struct BlockHeaderLight {
        bytes32 prev_block_hash;
        bytes32 inner_rest_hash;
        NearDecoder.BlockHeaderInnerLite inner_lite;
        bytes32 hash; // Computable
}
struct ExecutionStatus {
        uint8 enumIndex;
        bool unknown;
        bool failed;
        bytes successValue; // The final action succeeded and returned some value or an empty vec.
        bytes32 successReceiptId; // The final action of the receipt returned a promise or the signed transaction was converted to a receipt. Contains the receipt_id of the generated receipt.
}
struct ExecutionOutcome {
        bytes[] logs; // Logs from this transaction or receipt.
        bytes32[] receipt_ids; // Receipt IDs generated by this transaction or receipt.
        uint64 gas_burnt; // The amount of the gas burnt by the given transaction or receipt.
        uint128 tokens_burnt; // The total number of the tokens burnt by the given transaction or receipt.
        bytes executor_id; // Hash of the transaction or receipt id that produced this outcome.
        ExecutionStatus status; // Execution status. Contains the result in case of successful execution.
        bytes32[] merkelization_hashes;
}

struct ExecutionOutcomeWithId {
        bytes32 id; // The transaction hash or the receipt ID.
        ExecutionOutcome outcome;
        bytes32 hash;
}

struct MerklePathItem {
        bytes32 hash;
        uint8 direction; // 0 = left, 1 = right
}
struct MerklePath {
        MerklePathItem[] items;
}
```

其中包括輕客戶端證明給定交易或收據的執行結果所需的一切。

證明驗證可以分為兩步，執行結果根驗證和區塊默克爾根驗證。

###### 執行結果根驗證

如果交易或收據的結果根包含在塊“H”中，則“outcome_proof”包含“H”的塊哈希，以及其給定分片中執行結果的默克爾證明。 `H` 中的結果根可以重建為

```solidity
    bytes32 hash = _computeRoot(
            fullOutcomeProof.outcome_proof.outcome_with_id.hash,
            fullOutcomeProof.outcome_proof.proof
        );

        hash = sha256(abi.encodePacked(hash));

        hash = _computeRoot(hash, fullOutcomeProof.outcome_root_proof);

        if (
            hash != fullOutcomeProof.block_header_lite.inner_lite.outcome_root
        ) {
            return (false, "outcome merkle proof is not valid");
        }
```

這個結果根必須匹配`block_header_lite.inner_lite`中的結果根。

###### 區塊 Merkle 根驗證

回看區塊哈希可以通過以下方式從 LightClientBlockLiteView 計算出來

```solidity
hash = sha256(
            abi.encodePacked(sha256(abi.encodePacked(sha256(borsh(inner_lite), inner_rest_hash)), prev_block_hash)
        );
```

預期的區塊 merkle 根可以計算為

```solidity
 if (
            _computeRoot(
                fullOutcomeProof.block_header_lite.hash,
                fullOutcomeProof.block_proof
            ) != block_merkle_root
        ) {
            return (false, "block proof is not valid");
        }
```

它必須與輕客戶端頭的輕客戶端塊中的塊默克爾根匹配。

## 證明

###### 輕客戶端區塊 LightClientBlock
```solidity
struct LightClientBlock {
        bytes32 prev_block_hash;
        bytes32 next_block_inner_hash;
        BlockHeaderInnerLite inner_lite;
        bytes32 inner_rest_hash;
        OptionalBlockProducers next_bps;
        OptionalSignature[] approvals_after_next;
        bytes32 hash;   
        bytes32 next_hash;
}


```

###### 交易證明

```solidity
struct FullOutcomeProof {
        ExecutionOutcomeWithIdAndProof outcome_proof;
        MerklePath outcome_root_proof; 
        BlockHeaderLight block_header_lite;
        MerklePath block_proof;
}
```
