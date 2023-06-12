# EVM 鏈

  合約地址

【這裡獲取MAPO主網和測試網輕客戶端合約地址。】(/develop/light-client/README.md)



## 如何運行

MAPO 輕客戶端可以部署在與 EVM 區塊鏈兼容的合約上。 定期更新和保存MAPO區塊鏈的驗證者信息，用於驗證MAPO區塊鏈生成的證明。 以下是有關其工作原理的主要工作流程：

1. 輕客戶端合約使用 MAPO 區塊鏈信息進行部署和初始化，例如紀元大小、驗證者閾值和特定紀元（我們稱此紀元為**當前紀元**）的可信驗證者信息。
2. 輕客戶端從一個名為 **maintainer** 的鏈下程序接收 **current epoch** 的最後一個區塊頭。 塊頭可以由存儲的驗證器進行驗證。 驗證成功後，輕客戶端從區塊頭中獲取下一個 epoch 的驗證者信息。 輕客戶端然後存儲驗證器並將**當前紀元**更新到下一個紀元。 這個步驟每個 epoch 觸發一次，輕客戶端最多可以存儲最近 20 個 epoch 的驗證者。
3.輕客戶端只有存儲了對應epoch的validators信息，才能驗證某個MAPO區塊中receipt的有效性。 因此，如果一個應用程序想要使用 MAPO 輕客戶端來驗證證明，它可以首先通過從 MAPO 輕客戶端獲取可驗證的標頭範圍來檢查是否存儲了相應的驗證器。

## 如何驗證

證明數據的內容包括：

1.收據所在的MAPO區塊頭
2. 簽名驗證者的聚合 G2 公鑰
3.收據證明
4.收據在區塊中的索引
5.證明上述收據存在的證明

輕客戶端按照以下步驟驗證證明數據：

1. 計算出區塊頭的epoch number，根據epoch number獲取驗證者記錄。 如果不存在記錄，則返回錯誤。
2、使用驗證器驗證區塊頭的ecdsa簽名，使用驗證器和聚合的G2公鑰驗證區塊頭的BLS簽名。 這證明了區塊頭的有效性。 如果驗證失敗，則返回錯誤。
3. 一個 MAPO 區塊中的所有收據哈希構建一個 Merkle Patricia Tree，樹根記錄在區塊頭中。 輕客戶端從塊頭、密鑰索引和證明數據中的證明中通過樹根檢索樹葉。 然後它檢查樹葉是否等於證明數據中包含的收據的哈希值。 如果不是，則返回錯誤。

如果以上驗證均通過，證明數據有效。

＃＃ 證明

### 這是證明的數據結構。

```

struct TxReceiptRlp {
	// Transaction Type
    uint256 receiptType;
    // Logs RLP encoding
    bytes receiptRlp;
}

//Committee change information corresponds to extraData in blockheader
struct istanbulExtra {
    //Addresses of added committee members
    address[] validators;
    //The public key of the added committee member
    bytes[] addedPubKey;
    //G1 public key of the added committee member
    bytes[] addedG1PubKey;
    //Members removed from the previous committee are removed by bit 1 after binary encoding
    uint256 removeList;
    //The signature of the previous committee on the current header
    //Reference for specific signature and encoding rules
    bytes seal;
    //Information on current committees
    istanbulAggregatedSeal aggregatedSeal;
    //Information on the previous committee
    istanbulAggregatedSeal parentAggregatedSeal;
}

struct receiptProof {
	// the block header where the receipt exists
    blockHeader header;
    //the istanbulExtra struct
    istanbulExtra ist;
    // the aggregated G2 public key of the signed validators
    G2 aggPk;
    // the TxReceiptRlp struct
    TxReceiptRlp txReceiptRlp;
    // the index of the receipt in the block
    bytes keyIndex;
    // the proof to prove the existance of the above receipt
    bytes[] proof;
}
```