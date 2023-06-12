# MAP 維護者 - Compass

## 概括

Compass 是 MAP 協議跨鏈通信維護者的 Golang 實現。 它目前支持基於 EVM 的鏈之間的橋接。

新設計的指南針版本包含運行中繼節點所需的所有功能。 使用此工具，您可以在幾乎所有硬件平台上運行節點。

# 內容

## 快速開始

獲取可執行文件的推薦方法是從發布頁面下載它。

>如果您想從源代碼構建它，請查看下面的 [building](#building) 部分。

### 2.準備每個鏈的賬戶
為一些賬戶注資以便在每條鏈上發送交易，你想提供跨鏈服務。
最簡單的方法是為每個鏈使用相同的地址。

之後我們需要將帳戶導入指南針的密鑰庫。
使用私鑰是最簡單的方法，在終端中運行以下命令：
```zsh
compass accounts import --privateKey '********** your private key **********'
```

在導入過程中，系統會要求您輸入密碼。
密碼用於加密您的密鑰庫。您必須在解鎖帳戶時輸入密碼。

使用以下命令列出密鑰庫中導入的密鑰：
```zsh
compass accounts list
```

### 3.修改配置文件
從復制示例配置文件
```json
{
  "mapchain": {
    "id": "212",
    "endpoint": "http://18.142.54.137:7445",
    "from": "0xE0DC8D7f134d0A79019BEF9C2fd4b2013a64fCD6",
    "opts": {
      "mcs": "0x0ac4611305254cdd257beC56CB79CBeC720Cd02D",
      "lightnode": "0x000068656164657273746F726541646472657373",
      "http": "true",
      "gasLimit": "4000000000000",
      "maxGasPrice": "2000000000000",
      "syncIdList": "[34434]"
    }
  },
  "chains": [
    {
      "name": "pri-eth",
      "type": "ethereum",
      "id": "34434",
      "endpoint": "http://18.138.248.113:8545",
      "from": "0xE0DC8D7f134d0A79019BEF9C2fd4b2013a64fCD6",
      "opts": {
        "mcs": "0xcfc80beddb70f12af6da768fc30e396889dfce26",
        "lightnode": "0x80Be41aEBFdaDBD58a65aa549cB266dAFb6b8304",
        "http": "true",
        "gasLimit": "400000000000",
        "maxGasPrice": "200000000000",
        "syncToMap": "true"
      }
    }
  ]
}
```
相應地修改配置。
填寫每個鏈的帳戶。
### 4. 運行可執行文件
只需運行即可啟動並保持可執行文件運行：
```zsh
compass maintainer --blockstore ./block-eth-map --config ./config-mcs-erh-map.json
```
您將被要求輸入密碼以解鎖您的帳戶。（您在第 2 步輸入的密碼）
如果一切順利的話。 一切就緒

# 創建

創建需要 [Go](https://github.com/golang/go) 編譯器（1.16 或更高版本）

在 repo 的根目錄下

`make build`: Builds `compass` in `./build`.  
`make install`: Uses `go install` to add `compass` to your GOBIN.

# 維護者

根據配置文件中的信息同步各鏈中的區塊信息

從以下命令開始：
```zsh
compass maintainer --blockstore ./block-eth-map --config ./config.json
```

# 配置

配置文件是一個小的 JSON 文件。

```
{
  "mapchain": {
        "id": "0",                          // Chain ID of the MAP chain
        "endpoint": "ws://<host>:<port>",   // Node endpoint
        "from": "0xff93...",                // MAP chain address of maintainer
        "opts": {}                          // MAP Chain configuration options (see below)
    },
  "chains": []                              // List of Chain configurations
}

```

鏈配置採用以下形式：

```
{
    "name": "eth",                      // Human-readable name
    "type": "ethereum",                 // Chain type (Please see the following cousin for details)
    "id": "0",                          // Chain ID
    "endpoint": "ws://<host>:<port>",   // Node endpoint
    "from": "0xff93...",                // On-chain address of maintainer
    "keystorePath" : "/you/path/",      // 
    "opts": {},                         // Chain-specific configuration options (see below)
}
```

|鏈| 類型 |
| :-----------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ethereum | ethereum |
| bsc | bsc |
| goerli | eth2 |
| polygon | matic |
| near | near |
| klaytn | klaytn |

有關示例配置，請參閱“config.json.example”。

＃＃＃ 選項

由於 MAP 也是基於 EVM 的鏈，因此 **mapchain** 的選項也遵循以下選項
以太坊鏈支持以下附加選項：

```
{
    "mcs": "0x12345...",                                    // Address of the bridge contract (required)
    "maxGasPrice": "0x1234",                                // Gas price for transactions (default: 20000000000)
    "gasLimit": "0x1234",                                   // Gas limit for transactions (default: 6721975)
    "gasMultiplier": "1.25",                                // Multiplies the gas price by the supplied value (default: 1)
    "http": "true",                                         // Whether the chain connection is ws or http (default: false)
    "startBlock": "1234",                                   // The block to start processing events from (default: 0)
    "blockConfirmations": "10"                              // Number of blocks to wait before processing a block
    "egsApiKey": "xxx..."                                   // API key for Eth Gas Station (https://www.ethgasstation.info/)
    "egsSpeed": "fast"                                      // Desired speed for gas price selection, the options are: "average", "fast", "fastest"
    "lightnode": "0x12345...",                              // the lightnode to sync header
    "syncToMap": "true",                                    // Whether sync blockchain headers to Map
    "syncIdList": "[214]"                                   // Those chain ids are synchronized to the map，and This configuration can only be used in mapchain
    "event": "mapTransferOut(...)|depositOutToken(...)",    // MCS events monitored by the program, multiple with | interval，
                                                            // Here we give the events that need to be monitored，Map:mapTransferOut(bytes,bytes,bytes32,uint256,uint256,bytes,uint256,bytes) Near: 2ef1cdf83614a69568ed2c96a275dd7fb2e63a464aa3a0ffe79f55d538c8b3b5|150bd848adaf4e3e699dcac82d75f111c078ce893375373593cc1b9208998377
    "waterLine": "5000000000000000000",                     // If the user balance is lower than, an alarm will be triggered, unit ：wei
    "alarmSecond": "3000",                                  // How long does the user balance remain unchanged, triggering the alarm, unit ：seconds                                              
}
```
## 塊存儲

blockstore 用於記錄維護者處理的最後一個塊，因此它可以從中斷的地方繼續。

要禁用從塊庫加載，請指定“--fresh”標誌。 加上fresh flag，程序會從0高度開始執行，

另外，配置文件提供了“startBlock”選項，程序將從startBlock開始執行

## 密鑰庫

Compass 需要密鑰來簽署和提交交易，並識別鏈上的每個橋接節點。

要使用安全密鑰，請參閱“compass accounts --help”。 密鑰庫密碼可以與“KEYSTORE_PASSWORD”環境變量一起提供。

要導入外部以太坊密鑰，例如使用 geth 生成的密鑰，請使用 `compass accounts import --ethereum /path/to/key`。

要將私鑰導入為密鑰庫，請使用 `compass accounts import --privateKey key`。

# 鏈實現

- 以太坊（Solidity）：[合約](https://github.com/mapprotocol/contracts)
   Compass所需的 Solidity 合約。 包括用於部署的腳本。