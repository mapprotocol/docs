## 從源代碼開始創建

創建atlas需要Go（1.16或更高版本）和C編譯器。
你可以用你喜歡的軟件包管理器來安裝它們。
一旦安裝了這些，就可開始運行

```
    git clone https://github.com/mapprotocol/atlas.git
    cd atlas
    make atlas
```

## 運行 atlas

Going `atlas -h` can get help infos.

### 在 atlas 主網運行

```
$ atlas --datadir ./data0 console
```


### 在 Atlas 鏈單節點（私人）網絡上運行

要在單節點運行 g 實例，
用這些標記指令：

```
$ atlas --single --datadir ./data0  console
```
如果把  `--http` 標記到command，RPC服務器便會啟動，然後你便可以通過  `http://127.0.0.1:7445`擁有RPC服務器權限。