##如何成爲一個新的驗證者[進階版]	
爲了使你的資產更加安全，我們需要你設置一些必要的識別參數來成爲驗證者。	
我們還設置了相應的門檻，以便我們可以篩選出那些真正想爲鏈子做貢獻的人。當然	
當然，我們會給這些人以相應的獎勵。	
	
下面的步驟被視爲你的第一次操作，因爲你只需要執行一次以下操作	
來成爲驗證者。除非你註銷驗證人或取消相應的操作，否則你將不需要	
執行第二次操作，以避免浪費你的燃氣費。	
	
###第1步：創建賬戶	
	
在這一步中，你需要將你的身份信息轉移到相應的管理合約中，它將	
管理你的賬戶、鑰匙和元數據。	
	
這一步的目的是通過授權替代鑰匙來保持你鎖定的`MAP'更加安全，以用於簽署	
證明、投票、驗證。通過這樣做，你可以繼續參與協議，同時保持鑰匙對你鎖定的`MAP'的訪問。	
訪問你鎖定的`MAP'的鑰匙存放在倉庫裏。	
	
你需要 "createAccount "命令來執行上述操作，更多關於 "createAccount "命令的細節請見	
見[this](/develop/map-relay-chain/marker/AboutCommon.md#createaccount)。	
	
###第二步：授權	
	
授權一個地址代表該賬戶簽署共識信息。這個被授權的地址被稱爲簽名者。	
正如他的名字一樣，他只負責簽名，你的獎勵不會發給簽名者，而是發給在上一步創建的賬戶	
在上一步驟中創建的賬戶。	
	
###第三步：鎖定`MAP'。	
	
我們設定的驗證人的門檻是將1000,000 `MAP`鎖定在相應的管理智能合約中。	
	
這一部分鎖定的`MAP`將用於未來的懲罰，這也是當選的條件之一。	
	
你需要`lockedMAP`命令來執行上述操作，更多關於`lockedMAP`命令的細節請到	
見[this](/develop/map-relay-chain/marker/AboutCommon.md#lockedmap) .	
	
### 第四步：驗證器註冊	
	
這一步是註冊爲一個新驗證器的關鍵步驟。	
	
你需要`register`命令來執行上述操作，更多關於`register`命令的細節請見	
見[this](/develop/map-relay-chain/marker/AboutValidator.md#register)。	
	
在這一步，你將成功註冊爲驗證者。接下來，你可以嘗試爲自己投票。如何投票，請	
to see[this](/develop/map-relay-chain/marker/AboutVote.md#vote).	
	
###第五步：投票	
	
驗證者必須至少擁有總票數的0.001比例，才能被考慮參加選舉。所以驗證人	
不能沒有票數。	
	
我們可以用驗證人賬戶爲自己投票，也可以讓其他驗證人或投票人爲自己投票。	
	
我們在第三步中已經鎖定了1,000,000 MAP，現在爲自己投票是個不錯的決定	
	
## 高級例子	
	
### 啓動一個提供RPC服務的節點。	
	
你可以自己啓動一個提供RPC服務的節點，或者你可以使用	
我們提供的[RPC節點]（/develop/map-relay-chain/README.md）。	
	
- 克隆存儲庫	
	
```shell	
cd /Users/alex	
	
git clone https://github.com/mapprotocol/atlas.git && cd atlas	
git checkout -b release origin/release_v0	
```	
	
- Build	
	
```shell	
make atlas	
make marker	
```	
	
build生成的文件在build/bin目錄下	
	
###加入網絡	
	
首先你需要準備兩個keystore，一個用來staking的叫做account，一個用來挖礦的叫做signer	
	
`account` 的密鑰庫：account.json	
	
`signer` 的密鑰庫：signer.json	
	
如何構建Atlas [點此](/develop/map-relay-chain/getting-started/make-private-chain.md#build-four-nodes)	
	
如果想讓node在後台運行，不掛掉，可以結合使用nohup和&，或者screen之類的。	
下面我們將演示使用screen	
	
--miner.validator 用於指定`signer`的地址	
	
`--port 30321` 以確保端口在防火牆上打開	
	
```shell	
./atlas --datadir ./node --syncmode "full" --port 30321 --v5disc --mine --miner.validator 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 --unlock 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8	
	
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
	
###簽名者操作	
	
### 從簽名者處創建 ECDSA 簽名	
	
--target 用於指定`account`的地址	
	
--signerPriv 用於指定`signer`的私鑰	
	
```shell	
./marker makeECDSASignatureFromSigner --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25	
	
INFO [08-26	17:31:49.422] === makeECDSASignatureFromSigner ===
INFO [08-26	17:31:49.422] === signer  ===                          account=0x26654Eb0Bb935DCE4a34DAA3e14c67662a8Aa1f8
INFO [08-26	17:31:49.422] ECDSASignature                           result=0x59dff185...32f0d700
	
```	
	
### 生成簽名者證明	
	
--validator 用於指定`account`的地址	
	
--signerPriv 用於指定`signer`的私鑰	
	
```shell	
./marker generateSignerProof --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25	
	
INFO [08-26	17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26	17:32:23.955] === makeBLSProofOfPossessionFromSigner ===
INFO [08-26	17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1
	
```	
	
### 賬戶操作	
	
＃＃＃ 創建賬戶	
	
--keystore 用於指定`account`的地址	
	
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
	
### 簽名授權	
	
使用生成的簽名 (0x59dff185...32f0d700) 進行授權	
來自[從簽名者製作 ECDSA 簽名](/run/HowToBecomeANewValidatorAdvanced.md#make-ecdsa-signature-from-signer)	
	
--keystore 用於指定`account`的keystore (account.json)	
	
--signer 用於指定`signer`的地址鍵（signer.json）	
	
```shell	
./marker authorizeValidatorSignerBySignature --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --signer 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 --signature 0x59dff185...32f0d700	
	
INFO [07-08	14:55:00.015] authorizeValidatorSignerBySignature      signer=0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8    signature=0x59dff185...32f0d700
INFO [07-08	14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08	14:55:05.078] Transaction Success                      func=queryTx                  block Number=16
```	
	
### 鎖定MAP	
	
--keystore 用於指定`account`的keystore	
	
```shell	
./marker lockedMAP --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --lockedNum 1000000	
	
INFO [07-08	14:54:49.141] === Lock  gold ===
INFO [07-08	14:54:49.141] Lock  gold                               amount=1000000000000000000000000
INFO [07-08	14:54:49.148] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	14:54:49.150] Please waiting                           func=getResult                txHash =0x698140b0ad8677706a4d10d3c5c72f15a8e143623be84a6ed514990fe3f5e5f3
INFO [07-08	14:54:50.765] Transaction Success                      func=queryTx                 block Number=13
```	
	
### 驗證者通過證明註冊	
	
使用生成的證明 (0xf90149b8...0e56f0ab1) 註冊驗證器	
通過[生成簽名者證明](/run/HowToBecomeANewValidatorAdvanced.md#generate-signer-proof)	
	
--keystore 用於指定`account`的keystore	
	
```shell	
./marker registerByProof --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --proof 0xf90149b8...0e56f0ab1 --commission 150000	
	
INFO [08-26	17:32:25.055] registerValidatorByProof                 commission=150,000
INFO [08-26	17:32:25.548] === getTotalVotesForValidator ===        admin=0x73bc690093b9dd0400c91886184a60cc127b2c33
INFO [08-26	17:32:25.974] === getTotalVotesForValidator ===        result=0
INFO [08-26	17:32:26.011] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [08-26	17:32:26.119] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [08-26	17:32:27.241] Transaction Success                      func=queryTx                 block Number=194
```	
	
### 驗證	
	
至此我們已經完成了validator的註冊步驟，下面我們來驗證一下是否已經成為validator。	
	
```shell	
./marker getTotalVotesForEligibleValidators --rpcaddr http://127.0.0.1:7445	
	
INFO [07-08	15:10:03.301] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08	15:10:03.301] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=0
	
```	
	
從上面的結果可以看出，我們已經成為了驗證者。 但是我們的票數是 0，這使我們無法成為	
當選為可以參與區塊的驗證者，這不是我們想要的。 所以我們還需要投票給	
驗證器。	
	
###投票	
	
票數不能大於鎖定的票數。	
	
有關投票和選舉的更多信息，請點擊以下鏈接查看：	
	
[投票](/develop/map-relay-chain/getting-started/how-to-vote.md)	
[選舉](/develop/map-relay-chain/consensus/Election.md)	
	
--keystore 用於指定`account`的keystore	
	
```shell	
./marker vote --rpcaddr http://127.0.0.1:7445 --keystore ./account.json --target 0x73bc690093b9dd0400c91886184a60cc127b2c33 --voteNum 1000000	
	
INFO [07-08	15:11:13.693] === vote Validator ===                   admin=0x73bc690093b9dd0400c91886184a60cc127b2c33 voteTargetValidator=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote MAP Num=1000000
INFO [07-08	15:11:13.709] TxInfo                                   func=sendContractTransaction TX data nonce =4  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08	15:11:13.710] Please waiting                           func=getResult                txHash =0x8ec67797871ea313f7beea33900db8f680ddf2d01f35894c65e1212151729747
INFO [07-08	15:11:15.123] Transaction Success                      func=queryTx                 block Number=210
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
0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 # our signer	
]	
}	
```	
	
關於istanbul_getValidators的更多信息[點擊這裏](/sdk/ConsensusAPI.md#getvalidators)	