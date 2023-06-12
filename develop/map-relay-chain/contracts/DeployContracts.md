# 使用 Truffle 在 MAP 中繼鏈上部署創世合約

如何使用 Truffle 將智能合約部署到 MAP 中繼鏈。

## Truffle介紹

Truffle 是世界一流的區塊鏈開發環境、測試框架和資產管道，使用
以太坊虛擬機 (EVM)。 通過創建一個 Truffle 項目並編輯一些配置設置，您可以輕鬆地
在地圖鏈上部署您的項目。

要使用 Truffle 在 MAP 中繼鏈上部署，您應該擁有本地環境。 如果您更喜歡在沒有本地的情況下進行部署
環境，您可以使用 Remix 或 Replit 進行部署。

如果您是 Truffle 新手，請完成 [快速入門教程](https://trufflesuite.com/docs/truffle/quickstart) 以獲取更多信息
熟悉這個工具。

## 項目設置

設置項目文件夾

打開終端窗口，創建項目目錄，然後導航到該目錄。

## 初始化松露

初始化 truffle 會為您的 truffle 項目創建腳手架。

`truffle init`

## 配置部署設置

默認的 truffle.config.js 文件包含部署到以太坊網絡所需的連接，導入
HDWalletProvider，並連接到您的 .env 文件中的助記詞。 要部署地圖網絡，您需要更新此
配置文件指向不同的 Map 網絡，並添加一些特定於 Map 最佳實踐的細節。

# 更新 truffle-config.js 文件

在文本編輯器中打開 truffle-config.js 並通過以下示例配置其內容：
``` shell
  networks: {
   development: {
     host: "127.0.0.1",
     port: 7445,
     network_id: "*"
   },
   mapNetwork: {
      url: "https://poc2-rpc.maplabs.io"
      network_id: "*"
   }
  },
``` 

## 編譯合約

`truffle compile`

## 部署合約

運行以下命令之一部署到您選擇的 MAP 網絡。

`truffle deploy --network mapNetwork`


## 驗證器相關合約的部署

介紹部署validator相關的合約

你需要編譯你的 `atlas-contracts` 項目，我們需要關於 `atlas-contracts` 的 `bytecode` 來製作 `genesis.json` 文件。

1.在你喜歡的任何文件夾中下載`atlas-contracts`項目，使用這個命令`git clone https://github.com/mapprotocol/atlas-contracts.git`

2.假設你已經安裝了`node`，然後切換到項目文件初始化項目並使用這個命令`npm install`

3.使用`npm install truffle`下載truffle

4.使用truffle編譯工程，truffle編譯的命令為`truffle compile`

5.將在您的 atlas-contracts 項目中生成一個名為 build 的文件。 我們將使用這個文件來指定相應的參數。