##如何投票

## 介紹

我們可以用自己的驗證人賬戶爲自己投票，也可以讓其他驗證人或投票人爲自己投票。
下面我們將演示使用我們自己的驗證人賬戶爲自己投票，因爲這是最簡單的。

投票的數量，和你承諾的MAP數量是1比1。

在投票之前，你必須先成爲一個驗證者，如果你不是一個驗證者，請看[this](/run/HowToBecomeANewValidator.md)。

###第1步：創建賬戶

如果你以前做過這個步驟，請跳過它。

在這一步中，你需要將你的身份信息轉移到相應的管理合約中，它將管理你的賬戶、密鑰和元數據。

這一步的目的是通過授權替代鑰匙用於簽署證明、投票、驗證，使你鎖定的`MAP`更加安全。通過這樣做，你可以繼續參與協議，同時將訪問你的鎖定`MAP'的密鑰保存起來。

你需要 "createAccount "命令來執行上述操作，更多關於 "createAccount "命令的細節請參見[this](/develop/map-relay-chain/marker/AboutCommon.md#createAccount)。

示例：
```shell
./marker createAccount --rpcaddr http://127.0.0.1:7445 --name "validator" --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 

INFO [07-01|05:54:37.048] Create account                           func=createAccount address=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 name=validator
INFO [07-01|05:54:37.048] === create Account ===
INFO [07-01|05:54:37.056] TxInfo                                   func=sendContractTransaction TX data nonce =0  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-01|05:54:37.057] Please waiting                           func=getResult                txHash =0xbd9603b438fa5b98b894431111c6298605d47a12c8b508458482aa865f2d707c
INFO [07-01|05:54:42.082] Transaction Success                      func=queryTx                 block Number=360,085
INFO [07-01|05:54:42.083] === setName name ===
INFO [07-01|05:54:42.090] TxInfo                                   func=sendContractTransaction TX data nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-01|05:54:42.091] Please waiting                           func=getResult                txHash =0x8c68f99e1c7216a12d3e2ea12d293d150d94c2e0d0136504860e0f86c87cf77c
INFO [07-01|05:54:47.117] Transaction Success                      func=queryTx                 block Number=360,086
INFO [07-01|05:54:47.117] === setAccountDataEncryptionKey ===
INFO [07-01|05:54:47.125] TxInfo                                   func=sendContractTransaction TX data nonce =2  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-01|05:54:47.126] Please waiting                           func=getResult                txHash =0xed401e84bf7759f2559ba8d97bcee09bb2128cbfb798f3751a427a640944f60d
INFO [07-01|05:54:52.152] Transaction Success                      func=queryTx                 block Number=360,087
```

### 第 2 步：鎖定 `MAP`

如果您已鎖定足夠的`MAP`來投票，請跳過此步驟。

這一步的目的是從你鎖定的錢中扣除對應投票的部分給你想要投票的驗證者，所以你需要提前將你的`MAP`鎖定到對應的管理合約中。

您需要 `lockedMAP` 命令來執行上述操作，有關 `lockedMAP` 命令的更多詳細信息請參閱 [this](/develop/map-relay-chain/marker/AboutCommon.md#lockedMAP)。
示例
```shell
./marker lockedMAP --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --lockedNum 1000000

INFO [07-01|06:12:45.068] === Lock  gold ===
INFO [07-01|06:12:45.069] Lock  gold                               amount=1000000000000000000000000
INFO [07-01|06:12:45.084] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-01|06:12:45.085] Please waiting                           func=getResult                txHash =0x6f8c9ea1d4481ba26af144d4cf04b5275750f51b899a898e6c57a798fdbcceab
INFO [07-01|06:12:47.095] Transaction Success                      func=queryTx                 block Number=360,302
```

### 第三步：投票

這一步是投票。

當你到達這一步時，你可以投票給你最喜歡的驗證者。 您可以使用 `getTotalVotesForEligibleValidators` 子命令來查看所有當前驗證者及其投票。

示例
```shell
./marker getTotalVotesForEligibleValidators --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 

INFO [07-06|06:04:01.431] === getTotalVotesForEligibleValidators === admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-06|06:04:01.434] Validator:                               addr=0x36b77597430cc2C0DD090c67f77f67Fc28195b5D vote amount=1,056,438,467,567,624,843,076,833
INFO [07-06|06:04:01.434] Validator:                               addr=0x7cC3e34C2075D96ef69bF6445a234F6C5E244073 vote amount=1,056,438,467,567,624,843,076,833
INFO [07-06|06:04:01.434] Validator:                               addr=0x3B778BB4F460956E313Ba92484Eb84603A86a625 vote amount=1,046,438,467,567,624,843,076,833
INFO [07-06|06:04:01.434] Validator:                               addr=0x19b375EBB9eE1B21A592A933c6383Df49380046D vote amount=1,046,425,233,310,919,400,227,838
INFO [07-06|06:04:01.434] Validator:                               addr=0x19C560de95c222Ac4B0ad3093D66EF1Fe75640f4 vote amount=134,664,371,097,943,842,325,525
INFO [07-06|06:04:01.434] Validator:                               addr=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 vote amount=103,060,000,000,004,450,909,077
```

`vote`示例
```shell
./marker vote --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --target "0x078F684c7d3bf78BDbe8bEf93E56998442dc8099" --voteNum 100000

INFO [07-06|06:05:31.454] === vote Validator ===                   admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 voteTargetValidator=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 vote MAP Num=100000
INFO [07-06|06:05:31.478] TxInfo                                   func=sendContractTransaction TX data nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-06|06:05:31.479] Please waiting                           func=getResult                txHash =0x8f8724be7e7eb2ffb0034dee92dbe57fbd2534b057ba43adcc7c6be5b20b9ffa
INFO [07-06|06:05:32.083] Transaction Success                      func=queryTx                 block Number=446,614
```

當您完成投票後，您的投票將處於待定狀態。 在 epoch block 結束時，Elected validator 會自動激活與其相關的 pending 投票，您也可以使用 active 命令自行激活您的投票。 此活動操作需要大於您的待定投票時間。

您可以點擊[激活](./marker/AboutVote.md#activate)和[投票](/develop/map-relay-chain/marker/AboutVote.md#vote)查看詳情。

## 擴張：

### 案例：當您投票給尚未當選的驗證者時。

您的待定投票不會自動激活，直到驗證人被選出，驗證人將在下一個紀元自動激活您的投票。
因此，如果您不激活您的投票，您的投票將在第二個 epoch 驗證人當選後獲得收益。


