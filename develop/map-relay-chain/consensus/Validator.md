# 驗證者

## 什麼是驗證者？

Map 的共識協議由被選為驗證者的節點執行。 治理提案可以更改的活躍驗證者數量存在最大上限，目前設置為 100 個驗證者。 活躍的驗證者集是通過權益證明過程確定的，並在每個紀元結束時更新，固定週期約為 3 天。

## 登記

任何在 Locked `MAP` 中至少具有最低股份要求的帳戶，無論是投票還是非投票，都可以註冊驗證器。 如果指定了驗證密鑰，則可以將其用於此註冊。

## 驗證者的獎勵來源

validator 的獎勵來自每個會話系統獎勵的一定比例。 這個一定比例是在註冊時設置的，以後可以更改。

##註銷

- [註銷](/develop/map-relay-chain/marker/AboutValidator.md)

為了防止註銷時惡意佔用資源，我們將您的註銷請求置為pending狀態，並在epoch的最後一個區塊進行批量註銷。


## 執行

[Validators.sol](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/Validators.sol) 處理驗證者的註冊、註銷、質押、密鑰管理和紀元獎勵，以及 例程來管理帳戶的成員。