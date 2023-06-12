## 關於投票

關於投票命令的介紹。

###投票

您必須提前將足夠的`MAP`鎖定到`lockedGold`合約中，並將您的信息註冊到合約中。

減少您之前在 `LockedGold` 合約中註冊的帳戶對應的總數和未投票數。

增加“選舉”合約中驗證者的總票數和未決票數。

```shell
USAGE
  $ ./marker vote

OPTIONS
  --keystore                                                   your keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     Your target validator address
  
  --voteNum                                                    Your number of votes

                                                                                                            
EXAMPLES:
./marker vote
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000


RESPONSE:
success
or
Failed
```


### quicklyVote 快速投票

如果您還沒有創建賬號或鎖定地圖，您可以通過`quicklyVote`命令進行快速投票，
集成了 `createAccount` `lockedMAP`

請注意，您只能使用此命令一次。 無論命令成功與否，該命令只是對`createAccount` `lockedMAP` 命令進行打包組合，不具備復用的性質。

If you have not creat Account or locked the Map, you can quickly vote through the `quicklyVote` command, which
integrates the `createAccount` `lockedMAP`

Please note that you can only use this command once. No matter whether the command succeeds or fails, this command only packs and combines `createAccount` `lockedMAP` commands and does not have the nature of reuse.

```shell
USAGE
$ ./marker  quicklyVote   

OPTIONS
  --keystore                                                   your keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     Your target validator address
  
  --voteNum                                                    Your number of votes
  
  --lockedNum                                                  In order to vote to validator,
                                                               you will lock enough `MAP`.

                                                                                                            
EXAMPLES:
./marker vote
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000
--lockedNum 10000


RESPONSE:
success
or
Failed
```
### 啟用

在驗證者選舉中激活未決選票以開始賺取獎勵。 要作為選民獲得獎勵，需要在產生投票的紀元結束後的某個時間點激活您的未決投票。

這意味著將“帳戶”對“驗證者”的未決投票轉換為有效投票。

```shell
USAGE
$ ./marker activate   

OPTIONS
  --keystore                                                   your keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     Your target validator address
                                                               you can query it from 
                                                               `getTotalVotesForEligibleValidators`
                                                               command
  


                                                                                                            
EXAMPLES:
./marker activate
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"

RESPONSE:
success
or
Failed
```

### RevokePending 撤銷等待

撤銷 `validator` 的 `value` 未決投票。

此命令會將有投票權的 `MAP` 變成無投票權的 `MAP`。

增加與您之前在 `LockedGold` 合約中註冊的帳戶相對應的總數和無投票權的數量。

減少“選舉”合約中驗證者的總票數和未決票數。


```shell
USAGE
$ ./marker revokePending   

OPTIONS
  --keystore                                                   your keystore file path

  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     Your target validator address
                                                               you can query it from 
                                                               `getTotalVotesForEligibleValidators`
                                                               command
                                                               
  --lockedNum                                                   The `MAP` that you want to revoke from your penging votes for validator
  


                                                                                                            
EXAMPLES::
./marker revokePending
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--lockedNum 10000

RESPONSE:
success
or
Failed
```

### RevokeActive 撤銷激活

撤銷對“驗證者”的“價值”有效投票

此命令會將有投票權的 `MAP` 變成無投票權的 `MAP`。

增加與您之前在 `LockedGold` 合約中註冊的帳戶相對應的總數和無投票權的數量。

減少“選舉”合約中驗證者的總票數和有效票數。
```shell
USAGE
$ ./marker revokeActive

OPTIONS
  --keystore                                                   your keystore file path

  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     Your target validator address
                                                               you can query it from 
                                                               `getTotalVotesForEligibleValidators`
                                                               command
                                                               
  --lockedNum                                                   The `MAP` that you want to revoke from your active votes for validator
                                                                                                             
EXAMPLES:
./marker revokeActive
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--lockedNum 10000

RESPONSE:
success
or
Failed
```

