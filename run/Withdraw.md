## 驗證者撤回	
### 查詢被鎖定的MAP的數量	
	
你可以通過以下方法查詢有多少MAP被鎖定。	
	
```shell	
./marker getAccountTotalLockedGold --rpcaddr http://127.0.0.1:7445 --target 0x73bc690093b9dd0400c91886184a60cc127b2c33	
	
INFO [08-02	16:50:22.331] === getAccountTotalLockedGold ===        admin=0x0000000000000000000000000000000000000000 target=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02	16:50:22.333] result                                   lockedGold=1,000,000,000,000,000,000,000,000
```	
	
### 註銷驗證者	
	
此步驟不是必須的，但如果解鎖後您的鎖倉餘額不足10000000，請先註銷。	
註銷操作只能在註冊為驗證人後60天進行，註銷需要等待	
當前紀元的最後一個塊生效。	
	
```shell	
./marker deregister --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33	
	
INFO [08-02	16:52:40.688] === deregisterValidator ===
INFO [08-02	16:52:40.701] TxInfo                                   func=sendContractTransaction TX data nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02	16:52:40.702] Please waiting                           func=getResult                txHash =0xb904cae42c8e9d5481b0023b367a35a5be37802c1a9f7a9ff55835cd72044def
INFO [08-02	16:52:41.107] Transaction Success                      func=queryTx                 block Number=1211
```	
	
###解鎖MAP	
	
此步驟用於將 MAP 的狀態從鎖定狀態轉換為解鎖狀態	
	
```shell	
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore  /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --lockedNum 200000	
	
INFO [08-02	17:24:16.166] === unLock validator gold ===
INFO [08-02	17:24:16.166] unLock validator gold                    amount=200,000,000,000,000,000,000,000 admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02	17:24:16.180] TxInfo                                   func=sendContractTransaction TX data nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02	17:24:16.181] Please waiting                           func=getResult                txHash =0x70b5c777daa507ca6b6e6f9132e757f8804c4147f0f8542f71a456ecf079bffa
INFO [08-02	17:24:21.229] Transaction Success                      func=queryTx                 block Number=1591
```	
	
###查詢解鎖的MAP數量	
	
```shell	
./marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x73bc690093b9dd0400c91886184a60cc127b2c33	
	
INFO [08-02	17:24:28.336] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02	17:24:28.343] result:                                  index=0 values=100,000,000,000,000,000,000,000 timestamps=1,660,727,111
INFO [08-02	17:24:28.343] result:                                  index=1 values=200,000,000,000,000,000,000,000 timestamps=1,660,728,211
```	
	
查看輸出，我們可以看到我們現在有兩個解鎖資金，編號為 0 和 1。接下來我們將提取資金	
編號為 1。	
	
### 撤回MAP	
	
這一步會將獎勵的狀態從解鎖狀態兌換到餘額中，但是這一步需要	
解鎖 15 天后才能執行。	
	
```shell	
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --withdrawIndex 1	
	
INFO [08-02	17:24:44.848] === withdraw validator gold ===          admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02	17:24:44.858] TxInfo                                   func=sendContractTransaction TX data nonce =16  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02	17:24:44.859] Please waiting                           func=getResult                txHash =0x1d592838b4044a05072692ebc9e8ef68b3f14ff6faa690283f4f363bc6642639
INFO [08-02	17:24:46.072] Transaction Success                      func=queryTx                 block Number=1596
```	
	
### 查看餘額	
	
現在兌換的MAP已經在賬戶餘額中了，我們來驗證一下。	
	
```shell	
web3.fromWei(eth.getBalance("0x73bc690093b9dd0400c91886184a60cc127b2c33"), "ether")	
	
200000.9406	
	
	