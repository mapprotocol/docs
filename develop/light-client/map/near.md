# 近協議上的 MAPO 輕客戶端

## 合約地址

【這裡獲取MAPO主網和測試網輕客戶端合約地址。】(/develop/light-client/README.md)


## 如何運行

MAPO 輕客戶端是部署在 NEAR 區塊鏈上的合約。 更新並保存MAPO的驗證者信息
週期性的區塊鏈，並使用它們來驗證 MAPO 區塊鏈生成的證明。 這是關於的主要工作流程
怎麼運行的：

1. 使用 MAPO 區塊鏈信息部署和初始化輕客戶端合約，例如 epoch 大小，
    驗證者閾值，以及特定時期的可信驗證者信息（我們稱這個時期為**當前
    時代**）。
2. 輕客戶端從一個名為 **maintainer** 的鏈下程序接收 **current epoch** 的最後一個區塊頭。
    塊頭可以由存儲的驗證器進行驗證。 驗證成功後，輕客戶端獲取
    這
    來自下一個紀元的區塊頭的驗證者信息。 輕客戶端然後存儲驗證器和
    更新
    **當前紀元**到下一個紀元。 這個步驟每個 epoch 觸發一次，輕客戶端可以存儲
    驗證者
    最多最近 20 個紀元。
3.輕客戶端只有驗證者信息可以驗證某個MAPO塊中收據的有效性
    存儲相應的紀元。 所以如果一個應用程序想要使用 MAPO 輕客戶端來驗證證明，它
    能
    首先通過從 MAPO 燈中獲取可驗證的標頭範圍來檢查是否存儲了相應的驗證器
    客戶。

## 如何驗證

證明數據的內容包括：

1.收據所在的MAPO區塊頭
2. 簽名驗證者的聚合 G2 公鑰
3.收據證明
4.收據在區塊中的索引
5.證明上述收據存在的證明

輕客戶端按照以下步驟驗證證明數據：

1. 計算出區塊頭的epoch number，根據epoch number獲取驗證者記錄。 如果沒有記錄，
    返回一個錯誤。
2、使用validators驗證區塊頭的ecdsa簽名，使用validator和聚合的G2 public
    用於驗證區塊頭的 BLS 簽名的密鑰。 這證明了區塊頭的有效性。 如果驗證
    失敗，返回錯誤。
3. 一個MAPO區塊中的所有收據哈希構成一棵哈希樹Merkle Patricia Tree，樹根記錄在區塊中
    標頭。 輕客戶端通過樹根從塊頭、密鑰索引和
    證明數據中的證明。 然後它檢查樹葉是否等於包含在
    證明數據。 如果不是，則返回錯誤。

如果以上驗證均通過，證明數據有效。

## 證明

### 這是證明的數據結構。

ReceiptProof包括證明和收據證明。

```rust
pub struct ReceiptProof {
    // the block header where the receipt exists
    pub header: Header,
    // the aggregated G2 public key of the signed validators
    pub agg_pk: G2,
    // the receipt to prove
    pub receipt: Receipt,
    // the index of the receipt in the block
    pub key_index: Vec<u8>,
    // the proof to prove the existence of the above receipt
    pub proof: Vec<ProofEntry>,
}
```
