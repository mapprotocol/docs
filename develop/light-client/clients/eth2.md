# Eth2.0 PoS 輕客戶端
## 如何運行

Eth2.0輕客戶端是一種輕客戶端，目的是在MERGE後的執行層區塊中驗證收據。 這是
分為兩部分：

1. 實現主要輕客戶端邏輯的solidity合約部分；
2. 驗證信標塊頭和下一個同步委員會的最終性的預編譯合約部分，以及
    還
    驗證已證明的信標塊標頭的聚合 BLS 簽名。

以下是有關其工作原理的主要工作流程：

1.部署腳本通過beacon獲取基於可信區塊根的輕客戶端bootstrap信息
    chain RPC，同時腳本獲取一些其他必要的信息，然後部署和初始化輕客戶端
    與獲得的信息。
2. 輕客戶端從一個名為 **maintainer** 的鏈下程序接收輕客戶端更新。 這
    輕客戶端更新的數據結構如下：
     ```堅固
     結構 LightClientUpdate {
         BeaconBlockHeader attestedHeader； // 一個週期的同步委員會簽署的證明信標塊頭
         SyncCommittee nextSyncCommittee; // 下一期的同步委員會，可能為空
         bytes32[] nextSyncCommitteeBranch； // nextSyncCommittee 的 merkle 證明
         BeaconBlockHeader finalizedHeader； // 已驗證標頭狀態下的最終信標塊標頭
         bytes32[] finalityBranch； // finalizedHeader 的 merkle 證明
         BlockHeader finalizedExeHeader； // finalized beacon header對應的finalized execution layer block header
         bytes32[] exeFinalityBranch； // finalizedExeHeader 的證明
         同步聚合約步聚合； // 已證明的 beancon 標頭的聚合簽名
         uint64 簽名槽； // 聚合簽名在哪個槽中籤名
     }
     ```
    輕客戶端調用預編譯合約來驗證信標區塊頭和執行的最終性
    layer block header，同時驗證下一個sync committee是否存在，並驗證聚合BLS簽名
    已證明的塊頭。 一旦所有驗證通過，輕客戶端存儲下一個同步委員會，更新
    最終確定的信標鏈槽，以及最終確定的執行層區塊頭編號和哈希值。
3.每次**維護者**更新輕客戶端成功，最終確定的信標鏈插槽和最終確定
    更新執行層標題編號。 最新敲定的執行層header與
    上一個最終確定的標頭。 **維護者**應該將執行層塊頭更新為 light
    客戶端，輕客戶端將驗證塊頭的有效性並存儲哈希以供進一步證明
    確認。 維護者無法在填補空白之前進行下一次輕客戶端更新。
4. 輕客戶端只有hash在某個以太坊執行層區塊中才能驗證收據的有效性
    存儲塊的位置並填充它所在的間隙。 所以如果一個應用程序想要使用 eth2.0 輕客戶端來
    驗證證明，它可以首先通過獲取可驗證的標頭範圍來檢查相應的標頭是否可驗證
    來自 eth2.0 輕客戶端。

## 如何驗證

證明數據的內容包括：

1.回執所在的以太坊執行層區塊頭
2.收據證明
3.收據在區塊中的索引
4.證明上述收據存在的證明

輕客戶端按照以下步驟驗證證明數據：

1. 檢查輕客戶端是否記錄了證明數據中區塊頭的<區塊號，區塊哈希>對。
    如果不是，則返回錯誤。
2. 計算證明數據中區塊頭的哈希值，驗證其是否等於存儲的哈希值。 這證明了
    區塊頭的有效性。如果不是，則返回錯誤。
3. 一個以太坊執行層區塊中的所有收據哈希構建一個Merkle Patricia Tree，樹根為
    記錄在區塊頭中。 輕客戶端從塊頭通過樹根檢索樹葉，
    證明數據中的關鍵索引和證明。 然後它檢查樹葉是否等於收據的哈希
    包含在證明數據中。 如果不是，則返回錯誤。

如果以上驗證均通過，證明數據有效。

## 證明

### 這是證明的數據結構。

ReceiptProof包括證明和收據證明。

```solidity
   struct ReceiptProof {
      BlockHeader header;  // the block header where the receipt exists
      TxReceipt txReceipt; // the receipt to prove
      bytes keyIndex;      // the index of the receipt in the block
      bytes[] proof;       // the proof to prove the existence of the above receipt
   }
```
