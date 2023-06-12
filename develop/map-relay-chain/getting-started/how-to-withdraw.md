## 如何撤回投票 MAP

### 介紹

當您使用 vote 命令進行投票時，您的投票狀態為 pending。 只有噹噹前紀元的最後一個區塊生成時，您的投票狀態才會自動變為活躍。
接下來，按照這個文件，你將撤回你的投票

#### 如何撤回未激活的投票

因為你當前的投票是貢獻給`validator`（在下一個epoch），所以你的投票`MAP`會經歷兩種狀態`pending`和`active`狀態。

所以，您可以使用 `getPendingVotesForValidatorByAccount` 命令來檢查您是否有未激活的 `MAP`

示例
```shell
./marker getPendingVotesForValidatorByAccount --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445

INFO [07-07|13:32:03.891] === getPendingVotesForValidatorByAccount === admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-07|13:32:03.894] PendingVotes                             balance=100,000,000,000,000,000,000,000
```

如果您的投票`MAP`處於`pending`狀態，您可以使用'revokePending'命令撤回您的投票`MAP`。

示例
```shell
./marker revokePending --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445 --lockedNum 100000 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099

INFO [07-08|02:39:49.006] === revokePending ===                    admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|02:39:49.028] TxInfo                                   func=sendContractTransaction TX data nonce =14  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|02:39:49.029] Please waiting                           func=getResult                txHash =0x9b0c2bedf7a0fe1b29f698ac069c6f549073241bb08932f9592132908596989b
INFO [07-08|02:39:50.839] Transaction Success                      func=queryTx                 block Number=478,705
```

#### 如何撤回主動投票

如果您正在投票的 `MAP` 處於 `active` 狀態，您可以使用 `revokeActive` 命令撤回您的活躍投票 `MAP`。 如果執行此步驟，您撤回的選票將失去相應的獎勵。

首先，您可以使用 `getActiveVotesForValidatorByAccount` 命令查看您有多少活躍投票

示例
```shell
./marker getActiveVotesForValidatorByAccount --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099

INFO [07-08|03:45:18.228] === getActiveVotesForValidatorByAccount === admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|03:45:18.230] ActiveVotes                              balance=304,805,454,545,461,535,206,581
```

撤回已激活投票

示例
```shell
./marker revokeActive --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 --lockedNum  100000

INFO [07-08|05:47:48.065] === revokeActive ===                     admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|05:47:48.088] TxInfo                                   func=sendContractTransaction TX data nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|05:47:48.089] Please waiting                           func=getResult                txHash =0x24e55381f755affdc1e6916b8dbc6fa431623181490731dc77e18e01b26db0df
INFO [07-08|05:47:51.709] Transaction Success                      func=queryTx                 block Number=480,961
```

然後你也可以使用`getActiveVotesForValidatorByAccount`命令來確認是否撤銷成功

> 這兩個命令的詳細信息請點擊[revokePending](/develop/map-relay-chain/marker/AboutVote.md#revokepending) 或 [revokeActive](/develop/map-relay-chain/marker/AboutVote. md#revokeactive) 來查看。

> 注意：`revokePending` 和 `revokeActive` 命令都會把有投票權的 `MAP` 變成無投票權的 `MAP`
>
> 現在您的投票 `MAP` 狀態已經從 pending 狀態或 active 狀態變為 locking 狀態。
>
> 下一步同[如何提現鎖定MAP](/develop/map-relay-chain/how-to-withdraw.md#how-to-withdraw-locked-map)


## 如何提取鎖定的 MAP

###第一步：解鎖

您可以使用 `getAccountNonvotingLockedGold` 命令查看您有多少無投票權的地圖
示例
```shell
/marker getAccountNonvotingLockedGold --rpcaddr http://127.0.0.1:7445 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099

INFO [07-08|06:13:47.791] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|06:13:47.793] result                                   lockedGold=800,000,000,000,000,000,000,000
```

地圖鏈實施解鎖期，在提出解鎖請求後延遲 60 天才能從託管中恢復鎖定的`MAP`。

解鎖`MAP`，可以在解鎖期後撤回。 解鎖的`MAP`將顯示為“待取款”，直到解鎖期結束，之後可以通過“取款”命令取款。

你只能解鎖你的非投票`MAP`，如果你想取消你的投票`MAP`請參考[如何撤回MAP投票](/develop/map-relay-chain/how-to-withdraw.md#如何撤回MAP投票）

如果您是驗證者，您將無法解鎖 1000,000 `MAP` 作為驗證者所必需的質押。

你需要 `unlock` 命令來執行解鎖操作，更多關於 `unlock` 命令的細節請看[this]((/develop/map-relay-chain/marker/AboutCommon.md#unlockmap)。

示例
```shell
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --lockedNum 100000

INFO [07-08|06:30:08.880] === unLock validator gold ===
INFO [07-08|06:30:08.880] unLock validator gold                    amount=1,000,000,000,000,000,000,000 admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|06:30:08.903] TxInfo                                   func=sendContractTransaction TX data nonce =19  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|06:30:08.904] Please waiting                           func=getResult                txHash =0x42fca349f68c7018577f9f78e7b7560e12954c273bbb3fd1fe1cf88f127f2b01
INFO [07-08|06:30:11.117] Transaction Success                      func=queryTx                 block Number=481,469
```

您可以使用`get Pending Withdrawals` 命令查看當前可以提現的地圖
示例
```shell
./cmd/marker/marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x19b375ebb9ee1b21a592a933c6383df49380046d

INFO [07-08|06:25:27.544] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x19b375EBB9eE1B21A592A933c6383Df49380046D
INFO [07-08|06:25:27.546] result:                                  index=0 values=10,000,000,000,000,000,000,000 timestamps=1,658,286,912
```
**Timestamps是你可以提款的時間，以秒為單位**

### 第二步：退出

在解鎖期結束後撤回已解鎖的`MAP`。 這一步相當於將你鎖定在智能合約中的錢提現到你的賬戶地址。

您需要 `withdraw` 命令來執行取款操作，更多關於 `withdraw` 命令的細節請看[this](/develop/map-relay-chain/marker/AboutCommon.md#withdrawmap)。

至此，您鎖定的`MAP`已轉入您的賬戶地址。 您可以自行查詢。

示例
```shell
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --withdrawIndex 0

INFO [07-08|06:32:04.394] === withdraw validator gold ===          admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|06:32:04.406] TxInfo                                   func=sendContractTransaction TX data nonce =20  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|06:32:04.408] Please waiting                           func=getResult                txHash =0x4d874ff5792585ed91ad98379bf5243ff3383218a43967c3b9c5d1d633dc2340
INFO [07-08|06:32:06.218] Transaction Success                      func=queryTx                 block Number=481,492
```

提現後可以查看餘額，MAP推薦使用RPC接口。

示例
```shell
curl --location --request POST 'http://18.142.54.137:7445' \
--header 'Content-Type: application/json' \
--data-raw '{
    "jsonrpc": "2.0",
    "method": "eth_getBalance",
    "params": [
        "0x19b375ebb9ee1b21a592a933c6383df49380046d",
        "latest"
    ],
    "id": 1
}'
```
