# 標記 Marker

MAP 命令行界面簡介和安裝說明。

### 什麼是標記 Marker

Marker 包含製作 genesis.json 的工具和一些客戶端命令以更好地鏈接 Map 合約。

命令行界面允許用戶與MAP 協議智能合約進行交互。

它允許您使用命令行工具與MAP 協議和智能合約進行交互，而不是編寫 go 腳本。 您可能需要考慮的一些常見功能是幫助用戶參與選舉或鏈上治理、為驗證者投票或幫助用戶與多重簽名合約進行交互。

### 創建標記 Marker

```shell
Building `Marker` requires both a Go (version 1.14 or later) and a C compiler.You can install them using your favourite package manager.
git clone https://github.com/mapprotocol/atlas.git
cd atlas
make marker 
```

現在您可以運行“./build/bin/marker”來啟動標記或切換到 ./build/bin 路徑並運行“./marker”來啟動標記

### 命令

該工具被分解為具有以下模式的模塊和命令：
```shell
./marker　<command> <...args> 
```

Marker 工具假設用戶正在運行一個節點，他們有權在該節點上簽署交易。

請注意，我們的命令需要指定您的密鑰庫文件，因此您需要提前生成您的密鑰庫文件。

生成keystore文件的方法在這裡：

1. 您需要構建 `atlas` 項目，構建 `atlas` 需要 Go（1.14 或更高版本）和 C 編譯器。
2. 下載我們的 atlas 項目並使用此命令：`git clone https://github.com/mapprotocol/atlas.git` 在您最喜歡的文件夾中。
3.切換到atlas項目文件夾使用這個命令`go build -o ./favoritefolder/atlas *.go`會在-o指定的目錄下生成atlas client。
4. 使用您的客戶端像這樣生成您的密鑰庫文件。

```shell
USAGE
 ./atlas account new --keystore "keystore path"
EXAMPLES:
 ./atlas account new --keystore ./datadir/keystore
RESPONSE:
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:

Your new key was generated

Public address of the key:   0x929510A8b54D3a8d7943e2Cdb5BA1888F7Ab7C4a
Path of the secret key file: ./datadir/keystore/UTC--2022-03-15T02-11-43.837807000Z--929510a8b54d3a8d7943e2cdb5ba1888f7ab7c4a
The keystore has been stored in the directory specified by --keystore.
```

如果您已經有一個帳戶，您可以使用atlas客戶端進行轉換。 進入atlas控制台，使用命令：

```shell
USAGE
./atlas --dataDir "./data" console
web3.personal.importRawKey("your private key","your password")
EXAMPLES:
> web3.personal.importRawKey("eaffcd749482e68ca4ccf5a07a52eb7ff876ea461fbab642b2b57bcb33edb280","linjing")
"0xd2f9e7716cc88944e5ed9f675649532c80d765f8"
The keystore has been stored in the directory specified by --dataDir.
```

### 可選：運行完整節點

命令需要連接到MAP節點才能執行大部分功能。