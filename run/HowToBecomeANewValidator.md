## 介紹	
MAP是一個使用POS共識算法的鏈條，它的工作原理如下	
- 通過認捐MAP成爲驗證人	
- 每個驗證者投票選出每個紀元的驗證區塊的驗證者。	
- 在每個紀元結束時，根據選舉結果獲得獎勵。	
	
##先決條件	
	
###硬件要求	
	
1. 硬件配置	
	
MAP是一個權益證明網絡，它的硬件要求與工作證明網絡不同。	
Proof Of Stake共識對CPU的要求較低，但對網絡連接和延遲更敏感。	
以下是在MAP網絡上運行Validator的標準要求清單：	
	
- 內存：16GB RAM	
- CPU： 四核2.5GHz (64位)	
- 磁盤：256GB的SSD存儲，外加一個理想的輔助硬盤	
- 網絡： 至少100Mb輸入/輸出以太網與光纖互聯網連接，最好有冗餘連接和HA交換機	
	
2.  MAP 的數量	
	
你的賬戶需要至少有1,000,000MAP	
	
###軟件要求	
	
構建atlas需要git、Go（1.14或更高版本）和一個C語言編譯器。你可以用你喜歡的軟件包管理器來安裝它們。	
	
## 如何成爲一個新的驗證者	
	
爲了使你的資產更加安全，我們需要你設置一些必要的識別參數來成爲驗證者。	
我們也設置了相應的門檻，這樣我們就可以篩選出那些真正想爲鏈子做貢獻的人。	
當然，我們會對這些人給予相應的獎勵。	
	
下面的步驟被視爲你的第一次操作，因爲你只需要執行以下操作	
一次就可以成爲驗證者。除非你註銷驗證人或取消相應的操作，否則你將不需要	
執行第二次操作，以避免浪費你的汽油費。	
	
###第1步：創建賬戶	
	
在這一步，你需要將你的身份信息轉移到相應的管理合約中、	
它將管理你的賬戶、鑰匙和元數據。	
	
這一步的目的是通過授權替代鑰匙來保持你鎖定的`MAP'更加安全，以用於簽署	
證明、投票、驗證。通過這樣做，你可以繼續參與協議，同時保持鑰匙	
鎖定的`MAP'的訪問權。	
	
你需要`createAccount'命令來執行上述操作，更多關於`createAccount'命令的細節	
請看 [this](/develop/map-relay-chain/marker/AboutCommon.md#createaccount)。	
	
###第二步：授權	
	
授權一個地址代表該賬戶簽署共識信息。這個被授權的地址被稱爲簽名者。	
正如他的名字一樣，他只負責簽名，你的獎勵不會發放給簽名者、	
而是發給在上一步創建的賬戶。	
	
###第三步：鎖定`MAP'。	
	
我們設定的驗證人的門檻是將1000,000 `MAP`鎖定在相應的管理智能合約中。	
	
這一部分鎖定的`MAP`將用於未來的懲罰，這也是當選的條件之一。	
	
你需要`lockedMAP`命令來執行上述操作，更多關於`lockedMAP`命令的細節	
請看 [this](/develop/map-relay-chain/marker/AboutCommon.md#lockedmap) .	
	
### 第四步：驗證器註冊	
	
這一步是註冊爲一個新驗證器的關鍵步驟。	
	
你需要`register`命令來執行上述操作，更多關於`register`命令的細節請參見[this](/develop/map-relay-chain/marker/AboutValidator.md#register) 。	
	
在這一步，你將成功註冊爲驗證者。接下來，你可以嘗試爲自己投票。如何投票請看[this](/develop/map-relay-chain/marker/AboutVote.md#vote)。	
###第五步：投票	
	
驗證者必須至少擁有總票數的0.001比例，才能被考慮參加選舉。所以驗證人不能沒有票。	
	
我們可以用驗證人賬戶爲自己投票，也可以讓其他驗證人或投票人爲自己投票。	
	
我們在第三步中已經鎖定了1,000,000 MAP，現在爲自己投票是個不錯的決定	
	
## 示例	
	
	
### 啓動一個提供RPC服務的節點。	
	
你可以自己啓動一個提供RPC服務的節點，也可以使用我們提供的[RPC節點](/develop/map-relay-chain/Makalu-Proc/Proc-2.md#atlas-address)。	
	
- 克隆存儲庫	
```shell	
cd /Users/alex	
	
git clone https://github.com/mapprotocol/atlas.git && cd atlas	
git checkout -b release origin/release_v0	
```	
	
	
```shell	
make atlas	
make marker	
```	
build生成的文件在build/bin目錄下	
	
###加入網絡	
首先，你需要準備兩個keystore，一個用於staking，稱爲account，一個用於mining，稱爲signer	
	
帳戶 "的密鑰庫：account.json	
	
簽名者 "的密鑰庫：signer.json	
	
如何構建Atlas[點擊這裏](/develop/map-relay-chain/make-private-chain.md#build-four-nodes)	
	
如果你希望node在後臺運行而不掛起，你可以使用`nohup`和`&`組合，或者`screen`或類似的。下面我們將演示使用`screen`。	
	
--miner.validator用來指定`signer'的地址	
	
`--port 30321`以確保該端口在防火牆上是開放的	
	
	
```shell	
./atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 --unlock 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005	
	
INFO [08-01	16:15:45.369] Bumping default cache on mainnet         provided=1024 updated=4096
INFO [08-01	16:15:45.370] Maximum peer count                       ETH=50 LES=0 total=50
INFO [08-01	16:15:45.371] Set global gas cap                       cap=50,000,000
INFO [08-01	16:15:45.371] Allocated trie memory caches             clean=614.00MiB dirty=1024.00MiB
INFO [08-01	16:15:45.371] Allocated cache and file handles         database=/Users/t/data/atlas-1/atlas/chaindata cache=2.00GiB handles=5120
INFO [08-01	16:15:45.722] Opened ancient database                  database=/Users/t/data/atlas-1/atlas/chaindata/ancient readonly=false
......	
	
> net	
{	
listening: true,	
peerCount: 6,	
version: "22776",	
getListening: function(callback),	
getPeerCount: function(callback),	
getVersion: function(callback)	
}	
	
......	
	
INFO [08-01	16:40:40.796] Finalized                                func=Finalize        block=1 epochSize=50000 duration="333.422µs" lastInEpoch=false
INFO [08-01	16:40:40.797] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=5.391ms mgasps=0.000 number=1 hash=62d029..14f4c1 age=1mo4d23h dirty=7.07KiB
INFO [08-01	16:40:51.790] Finalized                                func=Finalize        block=2 epochSize=50000 duration="106.969µs" lastInEpoch=false
INFO [08-01	16:40:51.790] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=4.913ms mgasps=0.000 number=2 hash=c2b741..35869d age=1mo4d23h dirty=10.79KiB
INFO [08-01	16:40:53.892] Finalized                                func=Finalize        block=3 epochSize=50000 duration="104.285µs" lastInEpoch=false
INFO [08-01	16:40:53.893] Finalized                                func=Finalize        block=4 epochSize=50000 duration="105.568µs" lastInEpoch=false
```	
	
節點啟動後，會自動連接到其他節點，然後開始同步區塊。	
	
### 創建賬戶	
--keystore 用於指定`account`的keystore	
	
```shell	
./marker createAccount --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --name "validator"	
	
INFO [07-08	14:54:28.097] Create account                           func=createAccount address=0x73bC690093b9dD0400c91886184A60cC127b2c33 name=validator
INFO [07-08	14:54:28.097] === create Account ===
INFO [07-08	14:54:28.102] TxInfo                                   func=sendContractTransaction TX data nonce =0  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	14:54:28.103] Please waiting                           func=getResult                txHash =0xa8d9dfb5b74720a940e904972784225d605ebb111fe92b87184ccb3a54ffd452
INFO [07-08	14:54:30.726] Transaction Success                      func=queryTx                 block Number=9
INFO [07-08	14:54:30.726] === setName name ===
INFO [07-08	14:54:30.731] TxInfo                                   func=sendContractTransaction TX data nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	14:54:30.732] Please waiting                           func=getResult                txHash =0xf26f4f8b4a7f6ab44a70fe474162fe22b1bcd7694b4c1a42ed0b1a70d062d5ee
INFO [07-08	14:54:35.779] Transaction Success                      func=queryTx                 block Number=10
INFO [07-08	14:54:35.779] === setAccountDataEncryptionKey ===
INFO [07-08	14:54:35.784] TxInfo                                   func=sendContractTransaction TX data nonce =2  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	14:54:35.785] Please waiting                           func=getResult                txHash =0x430533536a6305b603c9abd6282a4fec359c7f42b8dbd78cf4d3f340ec9bf31b
INFO [07-08	14:54:40.224] Transaction Success                      func=queryTx                 block Number=11
```	
	
###授權	
簽名者賬戶： 0x98efa292822eb7b3045c491e8ae4e82b3b1ac005	
	
Signer PrivateKey: 8df920b696ef3f5fdcf01624405ea8236b2b4907766ad61d42ce877df05f8bca	
	
--keystore用於指定 "賬戶 "的keystore。	
	
--signerPriv用於指定 "signer "的私鑰。	
	
```shell	
./marker authorizeValidatorSigner --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --signerPriv 8df920b696ef3f5fdcf01624405ea8236b2b4907766ad61d42ce877df05f8bca	
	
INFO [07-08	14:55:00.015] === makeECDSASignatureFromSigner ===
INFO [07-08	14:55:00.015] === signer  ===                          account=0x98EFA292822eB7b3045C491e8ae4E82B3b1AC005
INFO [07-08	14:55:00.015] ECDSASignature                           result=0x712c3f5deaf6d5012ff94dc6a8c3f5bd3d5570ba1f878343fd48767f88fb317d76d39ca33e274eb245d617252d7a44cf048d8579cd896440183f5fbe9f65ca8800
INFO [07-08	14:55:00.015] authorizeValidatorSigner                 func=authorizeValidatorSigner address=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [07-08	14:55:00.015] === authorizeValidatorSigner ===
INFO [07-08	14:55:00.032] TxInfo                                   func=sendContractTransaction  TX data nonce =4  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08	14:55:05.078] Transaction Success                      func=queryTx                  block Number=16
```	
	
### 鎖定的MAP	
--keystore用於指定 "賬戶 "的keystore。	
	
```shell	
./marker lockedMAP --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --lockedNum 1000000	
	
INFO [07-08	14:54:49.141] === Lock  gold ===
INFO [07-08	14:54:49.141] Lock  gold                               amount=1000000000000000000000000
INFO [07-08	14:54:49.148] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	14:54:49.150] Please waiting                           func=getResult                txHash =0x698140b0ad8677706a4d10d3c5c72f15a8e143623be84a6ed514990fe3f5e5f3
INFO [07-08	14:54:50.765] Transaction Success                      func=queryTx                 block Number=13
```	
	
### 驗證者註冊	
--keystore用於指定 "賬戶 "的密鑰庫。	
	
--signerPriv用於指定 "signer "的私鑰。	
	
```shell	
./marker register --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --signerPriv 8df920b696ef3f5fdcf01624405ea8236b2b4907766ad61d42ce877df05f8bca --commission 150000	
	
INFO [07-08	15:09:53.230] === Register validator ===
INFO [07-08	15:09:53.230] === commission ===                        commission=150,000
INFO [07-08	15:09:53.233] === getTotalVotesForValidator ===        admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [07-08	15:09:53.234] === getTotalVotesForValidator ===        result=0
INFO [07-08	15:09:53.237] === makeBLSProofOfPossessionFromsigner ===
INFO [07-08	15:09:53.420] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	15:09:53.421] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [07-08	15:09:55.241] Transaction Success                      func=queryTx                 block Number=194
```	
	
###驗證	
到目前爲止，我們已經完成了驗證者的註冊步驟，現在我們來驗證它是否已經成爲一個驗證者。	
	
```shell	
./marker getTotalVotesForEligibleValidators --rpcaddr http://127.0.0.1:7445	
	
INFO [07-08	15:10:03.301] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=0
	
```	
從上述結果可以看出，我們已經成爲一個驗證者。但我們的票數爲0，這使我們無法	
被選爲可以參與區塊的驗證人，這不是我們想要的。所以我們還需要對驗證人進行投票。	
	
###投票	
票數不能大於鎖定的票數。	
	
關於投票和選舉的更多信息，請點擊下面的鏈接查看：	
	
[投票](/develop/map-relay-chain/how-to-vote.md)	
[選舉](/develop/map-relay-chain/consensus/Election.md)	
	
--keystore用於指定 "賬戶 "的keystore。	
	
```shell	
./marker vote --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --voteNum 1000000	
	
INFO [07-08	
INFO [07-08	
INFO [07-08	
INFO [07-08	
```	
	
### 驗證	
現在一起來驗證投票是否成功	
	
```shell	
./marker getTotalVotesForEligibleValidators --rpcaddr http://127.0.0.1:7445	
	
INFO [07-08	15:21:45.881] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:21:45.881] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:21:45.881] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:21:45.881] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:21:45.881] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=1,000,000,000,000,000,000,000,000
	
```	
從結果來看，我已經成功投給了自己，但還不夠。	
我們需要在下一個epoch調用RPC來最終確定我們是否被選為可以參與的驗證人	
區塊生成，就像下面這樣：	
	
```shell	
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":[],"id":1}' http://127.0.0.1:7445	
	
# Output	
{	
jsonrpc: "2.0",	
id: 1,	
result: [	
0xb4e1bc0856f70a55764fd6b3f8dd27f2162108e9,	
0x7a3a26123dbd9cfefc1725fe7779580b987251cb,	
0x7607c9cdd733d8cda0a644839ec2bac5fa180ed4,	
0x65b3fee569bf82ff148bdded9c3793fb685f9333,	
0x98efa292822eb7b3045c491e8ae4e82b3b1ac005 # our signer	
]	
}	
```	
關於istanbul_getValidators的更多信息[點擊這裏](/sdk/ConsensusAPI.md#getvalidators)	