# 將交易所與 MAP 中繼鏈集成

#＃ 概述

本文檔的目的是簡要概述如何與 EVM 兼容的 MAP 中繼鏈集成。 對於已經支持 ETH 的團隊，支持 MAP 中繼鏈就像啟動一個 MAP 中繼鏈節點 (atlas)（它具有與 go-ethereum 相同的 API）並在構建交易時填充 MAP 中繼鏈 ID (22776) 一樣簡單。

## 使用 MAP 中繼鏈端點進行集成

### 運行一個圖集節點

您可以從[源代碼](https://github.com/mapprotocol/atlas) 或[release versin](https://github.com/mapprotocol/atlas/releases) 獲取它。

來自源代碼：

```
// make sure the golang env

git clone https://github.com/mapprotocol/atlas.git
cd atlas
git checkout release_v1
make atlas

```
然後在後台啟動一個帶有RPC服務的節點，使用`./build/bin/atlas -h`獲取更多細節。
```
./build/bin/atlas --datadir ./data --gcmode "archive" --syncmode "full" --port 28360 --v5disc --http --http.addr "0.0.0.0" --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" --http.vhosts "*" 
```

## 與 Atlas 交互

與 Atlas 節點交互與與 [go-ethereum](https://geth.ethereum.org/) 交互相同。 您可以在 [此處](/sdk/RPC-API.md) 找到 Atlas API 的參考資料。

請注意，默認情況下 personal_ 命名空間處於關閉狀態。 要打開它，您需要通過適當的命令行。


### Java SDK 和 Web3.js

您可以使用 [Java SDK](https://github.com/web3j/web3j) 和 [web3.js](https://web3js.readthedocs.io/en/v1.2.9/) 庫與圖集交互 .

如果您計劃使用 golang 從 Atlas 中提取數據到您自己的系統中，我們建議使用我們的自定義 [ethclient](https://github.com/mapprotocol/compass/tree/main/pkg/ethclient)。

### 構建交易

MAP 中繼鏈交易與標準 EVM 交易相同，只有一個例外：

     它們必須使用 MAP 中繼鏈 ChainID (22776) 進行簽名。


出於開發目的，MAP 中繼鏈支持所有流行的以太坊工具，如“MetaMask 和 Remix”、“Truffle”和“Hardhat”，因此熟悉以太坊和 Solidity 的開發人員可以感到賓至如歸。

我們兼容以太坊eip1559的改進，將最低基礎費用設置為100GWei。

ps: MAP Relay Chain consensus provides fast and irreversible finality with 5 seconds. To query the most up-to-date finalized block, query any value (i.e. block, balance, state, etc.) with the latest parameter.