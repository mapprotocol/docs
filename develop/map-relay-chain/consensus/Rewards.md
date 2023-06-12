# 紀元獎勵

圖鏈紀元獎勵介紹及目標獎勵發放時間表。

## 什麼是 Epoch 紀元獎勵？

Epoch Rewards 類似於其他區塊鏈中熟悉的塊獎勵概念，鑄造和分發新的
MAP 的單位作為區塊被生產出來，以創造多種激勵。

## Epoch 紀元獎勵在紀元的最後一個區塊支付，用於：

1. 驗證者的分佈式獎勵

2. 將獎勵分配給 Locked MAP 的持有者投票選出驗證者的驗證者

3. 向社區基金支付協議基礎設施贈款

每個epoch都會獲得固定的獎勵。

- 前 2 年，`EpochReward`= 833,333 `MAP`。


## 獎勵發放

支付金額在每個時期結束時通過三個步驟確定。

### 步驟1

第一步，社區基金將獲得固定比例的獎勵

- `CommunityFundReward` = `CommunityFundMultiplier`*`EpochReward`
- `CommunityFundMultiplier` 默認值 = 0。

### 第2步

在第二步中，我們將統計每個驗證者的簽名數量並將其轉換為分數（`Sorce`>=0,<=1），即
最終會參與獎勵的計算。

如果驗證節點未能履行其職責，將通過懲罰機制對其進行懲罰。

因此每個驗證者將獲得獎勵：

- `ValidatorReceived` = `(`EpochReward`-`CommunityFundReward`)` * `StakingWeight` * `OwnActiveVotes / TotalActiveVotes` + `WorkWeight` * `OwnScore / TotalScores`。
  
- `StakingWeight` 驗證者的質押權重（StakingWeight>0，StakingWeight<=1，默認值為1）
  
- `WorkWeight` 驗證者的工作權重（WorkWeight = 1 - StakingWeight）。

- `OwnActiveVotes` 當前驗證者激活的投票數。

- `TotalActiveVotes` 所有驗證者收到的有效投票。

- `OwnScore` 當前驗證者獲得的分數。 你的節點在線時間會直接影響這個參數。
   如果你的這個參數小於其他驗證器，你將受到懲罰，這將削減你的獎勵。 （0 <= 自己的分數 <= 1）

- `TotalScores` 所有驗證者的分數總和。

分數按以下公式計算：

- `NewScore` = `UptimeScore` \* `AdjustmentSpeed`+`OldScore` \* `(1 - `AdjustmentSpeed`)`
  
- `UptimeScore` 這是在 `TotalMonitoredBlocks` 完成的工作得分。

   - `TotalMonitoredBlocks` 是我們監控該時期正常運行時間的區塊總數。
  
   - `TotalMonitoredBlocks` 值範圍是 `[EpochFirstBlock + lookbackWindowSize(defult = 12) -1,EpochLastBlock - BlocksToSkipAtEpochEnd(defult = 2)]`
  
   - `lookbackWindowSize` 一個關於 lookbackWindow 的固定值，用於檢查驗證者是否已在固定時間間隔內簽名。
   - 如果您在`[NowBlockNumber-lookbackWindow ,NowBlockNumber]`簽到成功，我們將把您當前的區塊視為一次成功的作品。
  
   - `BlocksToSkipAtEpochEnd` 表示在監控窗口中從結束時跳過的塊數
     時代

     - 關於目前我們跳過塊：
  
     - lastBlock => 它的 parentSeal 在下一個紀元的 firstBlock 上
  
     - lastBlock - 1 => parentSeal 在 lastBlockOfEpoch 上，但 validatorScore 是用 lastBlockOfEpoch 計算的，並且
       更新分數之前

     - （可以計算 lastBlock-1，但更難實現）
  
   - 關於要監視的第一個塊：
  
     - 噹噹前 lookbackWindow 跨越紀元邊界時，我們無法監控正常運行時間。
  
     - 因此，要監視的第一個塊是從 firstBlockOfEpoch 開始的回溯窗口的最後一個塊。
  
   - 所以 `UptimeScore` = `SignedBlocks ` / `TotalMonitoredBlocks`。
  
     - `SignedBlocks ` 驗證器簽名的塊數，每次生成塊時都會檢查。
     - 首先，檢查當前塊是否在 `BlocksToSkipAtEpochEnd` 中。
     - 第二，檢查當前區塊審核週期是否已簽署。 如果存在，簽名將被視為
       作為成功
     - 如果第一步和第二步成功，`SignedBlocks` 將加 1。
- `AdjustmentSpeed` 調整因子。這種方式將鼓勵驗證器穩定。（'AdjustmentSpeed' 默認值為 0.2）。
- `OldScore`。 這是這個驗證者最後一個 epoch 的分數。（在第一個分發 epoch 獎勵 OldScore 是 0）

### 第 3 步

在第三步中，由於驗證人會將獎勵分配給投票者，因此實際驗證人將獲得獎勵：

- `ValidatorActualReceived` =`ValidatorReceived` * `佣金`*` OwnScore`。
- `Commission` 驗證器根據 `ValidatorReceived` 得出的比例值。

驗證人的選民將收到：

-`VotersReward` = `ValidatorReceived` - `ValidatorActualReceived`。

這些獎勵（`VotersReward`）將返回到投票池中。 這個操作相當於增加了每個選民的選票數。 選民從投票池中投票並獲得相應的獎勵。

將獲得每個投票者的獎勵根據選民對驗證者的投票比例。

## 擴張：

### 案例一：Validator 在其服務期間被註銷。

在這種情況下，我們獎勵時不考慮他，其餘驗證者將平分他的獎勵。

### 案例二：Validator 重新註冊。

當一個驗證者被註銷時，選民仍然可以按照他們的意願取消對被註銷的驗證者的投票。

如果投票人不撤回投票，投票人的投票將留在投票區，等待驗證人重新註冊，
這些票將再次返回給驗證者。

＃＃ 執行

[EpochRewards.sol](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/EpochRewards.sol) 管理計算紀元獎勵。