## common commond

Introduces the query interface, registered account and locking `MAP`

### CreateAccount

create an account

```shell
USAGE
  $ ./marker createAccount

OPTIONS
  --keystore                                                   Keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 

  --name                                                you can marker your account
                                                              like "validator" or "voter"
                                                              and so on that you want 

EXAMPLES:
./marker createAccount
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--name "validator"

RESPONSE:
success
or
Failed
```

### LockedMAP

Locks map to be used for voting or register validator

```shell
USAGE
  $ ./marker lockedMAP

OPTIONS
  --keystore                                                   Keystore file path

  --rpcaddr                                                    HTTP-RPC server address 

  --lockedNum                                                  The `MAP` you want to lock
                                                              
                                                            

EXAMPLES:
./marker lockedMAP
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--lockedNum 1000000

RESPONSE:
success
or
Failed
```

### UnlockMAP

Unlocks `MAP` that becomes withdrawable after the unlocking period.

```shell
USAGE
  $ ./marker unlockMap

OPTIONS
  --keystore                                                   Keystore file path

  --rpcaddr                                                    HTTP-RPC server address 

  --lockedNum                                                   The `MAP` you want to unlock
                                                              
                                                            

EXAMPLES:
./marker unlockMap
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--lockedNum 1000000

RESPONSE:
success
or
Failed
```

### RelockMAP

Relocks map that has been unlocked but not withdrawn.

```shell
USAGE
  $ ./marker relockMAP

OPTIONS
  --keystore                                                   Keystore file path

  --rpcaddr                                                    HTTP-RPC server address
                                                                                                                       
  --lockedNum                                                   The `MAP` you want to Relocks
                                                              
  --relockIndex                                                from `getPendingWithdrawals` command                                                     

EXAMPLES:
./marker relockMAP
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
 --lockedNum 100000
--relockIndex 1

RESPONSE:
success
or
Failed
```

### WithdrawMap

Withdraws `MAP` that has been unlocked after the unlocking period has passed.

```shell
USAGE
  $ ./marker withdrawMap

OPTIONS
  --keystore                                                   Keystore file path

  --rpcaddr                                                    HTTP-RPC server address
                                                              
  --withdrawIndex                                             from `getPendingWithdrawals` command                                                     

EXAMPLES:
./marker withdrawMap
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--withdrawIndex 1

RESPONSE:
success
or
Failed
```

### GetNumRegisteredValidators

Returns the number of registered validators

```shell
USAGE
  $ ./marker getNumRegisteredValidators

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address
                                                                                                               

EXAMPLES:
./marker getNumRegisteredValidators --rpcaddr http://127.0.0.1:7445

RESPONSE:
100
```

### GetTopValidators

Returns the top n validator members for a particular validator.

```shell
USAGE
  $ ./marker getTopValidators

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --topNum                                                     the top number validators
                                                               you want to konw,your can
                                                               konw 'topNum' by
                                                               `getNumRegisteredValidators`
                                                               commond                                                                                                      

EXAMPLES:
./marker GetTopValidators
--rpcaddr http://127.0.0.1:7445
--topNum 6

RESPONSE:
INFO [03-14|17:04:48.606] === getTopValidators ===                 admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:04:48.636] Validator:                               index=0 addr=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:04:48.636] Validator:                               index=1 addr=0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF
INFO [03-14|17:04:48.636] Validator:                               index=2 addr=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11
INFO [03-14|17:04:48.636] Validator:                               index=3 addr=0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5
```

### GetTotalVotesForEligibleValidators

Returns lists of all validator validators and the number of votes they've received.

```shell
USAGE
  $ ./marker getTotalVotesForEligibleValidators

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address                                                                                                  

EXAMPLES:
./marker getTotalVotesForEligibleValidators
--rpcaddr http://127.0.0.1:7445


RESPONSE:
INFO [03-14|17:06:13.020] === getTotalVotesForEligibleValidators === admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:06:13.049] Validator:                               addr=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15 vote amount=70,350,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF vote amount=70,322,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 vote amount=70,322,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5 vote amount=70,322,500,000,000,000,000,000,000
INFO [03-14|17:06:13.049] Validator:                               addr=0x81f02Fd21657DF80783755874a92c996749777Bf vote amount=32,968,000,000,000,000,000,000,000

```

### getTotalVotes

Returns the total votes received across all validators.

```shell

USAGE
  $ ./marker getTotalVotes

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address                                                                                                  

EXAMPLES:
./marker getTotalVotes
--rpcaddr http://127.0.0.1:7445

RESPONSE:
INFO [03-14|17:07:24.458] === getAccountLockedGoldRequirement ===  admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:07:24.487] result                                   getTotalVotes=315,096,000,000,000,000,000,000,000
```

### GetValidatorEligibility

Returns whether or not a validator is eligible to receive votes.

```shell
USAGE
  $ ./marker getValidatorEligibility

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     Your target validator address
  
EXAMPLES:
./marker getValidatorEligibility
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:10:27.990] === getValidatorEligibility ===          admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:10:28.018] === result ===                           bool=true
```

### GetValidator

Returns the validator`s information.

```shell

USAGE
  $ ./marker getValidator

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     Your target validator address
  
EXAMPLES:
./marker getValidator
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:12:22.303] === getValidator ===                     admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:12:22.332]                                          ecdsaPublicKey=0x2b5e2a3beacf839d1ec74fd00f4388d4b813eac26b26ab4859003473b286650a
INFO [03-14|17:12:22.332]                                          BlsPublicKey=0x1fd39f1fbcad8e3188442ea31dee662389599751f8e73b99215cefc2e0003f81
INFO [03-14|17:12:22.332]                                          Score=1
INFO [03-14|17:12:22.332]                                          Signer=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:12:22.332]                                          Commission=0.1
INFO [03-14|17:12:22.333]                                          NextCommission=0
INFO [03-14|17:12:22.333]                                          NextCommissionBlock=0
INFO [03-14|17:12:22.333]                                          SlashMultiplier=1
INFO [03-14|17:12:22.333]                                          LastSlashed=0
```

### getValidatorRewardInfo

Returns the awards of the last epoch

```shell

USAGE
  $ ./marker getValidatorRewardInfo

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
  
EXAMPLES:
./marker getValidatorRewardInfo
--rpcaddr http://127.0.0.1:7445

RESPONSE:
INFO [03-14|17:13:42.872] === getReward ===                        cur_epoch=389 epochSize=20 queryBlockNumber=7760 validatorContractAddress=0x000000000000000000000000000000000000D012 admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:13:42.874]                                          validator=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15 reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5 reward=19,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874]                                          validator=0x81f02Fd21657DF80783755874a92c996749777Bf reward=9,999,999,999,999,999,999,999
INFO [03-14|17:13:42.874] === END === 
```

### GetPendingVotesForValidatorByAccount

Returns the pending votes for `validator` made by `account`.

```shell

USAGE
  $ ./marker getPendingVotesForValidatorByAccount

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     Your target validator address
  
EXAMPLES:
./marker getPendingVotesForValidatorByAccount
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:16:39.474] === getPendingVotesForValidatorByAccount === admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:16:39.501] PendingVotes                             balance=0
```

### GetPendingInfoForValidator

Returns the pending votes for `validator` made by `account` And the pending Epoch.

```shell
USAGE
  $ ./marker getPendingInfoForValidator

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     Your target validator address
  
EXAMPLES:
./marker GetPendingInfoForValidator
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:20:49.046] === getPendingInfoForValidator ===       admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:20:49.074] getPendingInfoForValidator               Value=0 Epoch=0
```

### GetActiveVotesForValidatorByAccount

Returns the active votes for `validator` made by `account`.

```shell
USAGE
  $ ./marker getActiveVotesForValidatorByAccount

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     Your target validator address
  
EXAMPLES:
./marker getActiveVotesForValidatorByAccount
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:24:41.690] === getActiveVotesForValidatorByAccount === admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:24:41.717] ActiveVotes                              balance=71,502,344,612,553,670,006,133,714
```

### GetValidatorsVotedForByAccount

Returns the validators that `account` has voted for.

```shell
USAGE
  $ ./marker getValidatorsVotedForByAccount

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     Your target validator address
  
EXAMPLES:
./marker getValidatorsVotedForByAccount
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:26:57.697] === getValidatorsVotedForByAccount ===   admin=0x0000000000000000000000000000000000000000
INFO [03-14|17:26:57.726] validator                                Address=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
```

### GetAccountTotalLockedGold

Returns the total amount of locked `MAP` for an account.

```shell
USAGE
  $ ./marker getAccountTotalLockedGold

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                 that the account you want to query(including yourself)
  
EXAMPLES:
./marker getAccountTotalLockedGold
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:28:53.815] === getAccountTotalLockedGold ===        admin=0x0000000000000000000000000000000000000000 target=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:28:53.844] result                                   lockedGold=72,036,161,827,847,066,039,664,690
```

### GetAccountNonvotingLockedGold

Returns the total amount of non-voting locked `MAP` for an account.

```shell

USAGE
  $ ./marker getAccountNonvotingLockedGold

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     targetAddress that the account you want to query(including yourself)
  
EXAMPLES:
./marker getAccountNonvotingLockedGold
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:30:55.164] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:30:55.192] result                                   lockedGold=0
```

#### GetAccountLockedGoldRequirement

Returns the current locked `MAP` balance requirement for the supplied account.

```shell

USAGE
  $ ./marker getAccountLockedGoldRequirement

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
 
  
EXAMPLES:
./marker getAccountLockedGoldRequirement
--rpcaddr http://127.0.0.1:7445

RESPONSE:
INFO [03-14|17:32:15.948] === getAccountLockedGoldRequirement ===  admin=0x0000000000000000000000000000000000000000 target=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:32:15.976] result                                   GoldRequirement=1,000,000,000,000,000,000,000,000
```

### GetPendingWithdrawals

Returns the pending withdrawals from unlocked `MAP` for an account.

```shell

USAGE
  $ ./marker getPendingWithdrawals

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     targetAddress that the account you want to query(including yourself)
  
EXAMPLES:
./marker getPendingWithdrawals
--rpcaddr http://127.0.0.1:7445
--target 0x1c0edab88dbb72b119039c4d14b1663525b3ac15

RESPONSE:
INFO [03-14|17:33:18.696] === getPendingWithdrawals ===            admin=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15 target=0x1c0eDab88dbb72B119039c4d14b1663525b3aC15
INFO [03-14|17:33:18.724] nil 
```

### Transfer

transfer

```shell

USAGE
  $ ./marker transfer

OPTIONS
  --keystore                                                   Keystore file path of sender

  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     recipient`s address
  
  --amount                                                     transfer amount, unit (wei)
  
EXAMPLES:
./marker transfer --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/keystore/UTC--2022-05-31T03-33-25.405082000Z--3b778bb4f460956e313ba92484eb84603a86a625  --target 0x7cc3e34c2075d96ef69bf6445a234f6c5e244073 --amount 10

RESPONSE:
INFO [08-30|10:56:11.048] Tx Info                                  func=sendContractTransaction from=0x3B778BB4F460956E313Ba92484Eb84603A86a625 to=0x7cC3e34C2075D96ef69bF6445a234F6C5E244073 value=10 nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =212
INFO [08-30|10:56:11.144] Please waiting                           func=getResult                txHash =0x55beb566d735e7b46e61d85f23e52b0958777ba4bbd2332244b3a2e5eb22e137
INFO [08-30|10:56:12.234] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|10:56:13.328] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|10:56:15.748] Transaction Success                      func=getResult               number=668,236
INFO [08-30|10:56:15.748] transfer success                         from =0x3B778BB4F460956E313Ba92484Eb84603A86a625 to=0x7cC3e34C2075D96ef69bF6445a234F6C5E244073 amount=10
```

### getAccountMetadataURL

Returns the metadata url of the account

```shell

USAGE
  $ ./marker getAccountMetadataURL

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     target address that the account you want to query(including yourself)
    
EXAMPLES:
./marker getAccountMetadataURL --rpcaddr http://127.0.0.1:7445 --target 0xef021f15d188ad28625517a8d73cd20ce743a32d

RESPONSE:
INFO [08-30|11:06:12.108] get account metadata url                 address=0xeF021f15D188ad28625517A8D73CD20cE743a32D url=https://www.example.com
```

### setAccountMetadataURL

Set the metadata url for the account

```shell

USAGE
  $ ./marker setAccountMetadataURL

OPTIONS
  --keystore                                                   Keystore file path of sender
                                                          
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     target address that the account you want to query(including yourself)
    
EXAMPLES:
./marker setAccountMetadataURL --rpcaddr http://127.0.0.1:7445 --keystore /Users/alex/data/keystore/UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d  --url https://www.metadata.com

RESPONSE:
INFO [08-30|11:09:53.016] set account metadata url                 address=0xeF021f15D188ad28625517A8D73CD20cE743a32D url=https://www.metadata.com
INFO [08-30|11:09:54.678] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000d010 value=<nil> nonce =5  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [08-30|11:09:55.092] Please waiting                           func=getResult                txHash =0xde15a1daee5e74c183f759c00cf51ba09c31c76c01362a25e2020b1c04fa9f7d
INFO [08-30|11:09:56.508] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|11:09:57.926] Please waiting, Transaction is in pending status func=getResult
INFO [08-30|11:09:58.350] Transaction Success                      func=getResult               number=61898
```

### getAccountName

Returns the name of the account

```shell

USAGE
  $ ./marker getAccountName

OPTIONS
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     target address that the account you want to query(including yourself)
    
EXAMPLES:
./marker getAccountName --rpcaddr http://127.0.0.1:7445 --target 0xef021f15d188ad28625517a8d73cd20ce743a32d

RESPONSE:
INFO [08-31|15:15:53.195] get name                                 address=0xeF021f15D188ad28625517A8D73CD20cE743a32D name=validator
```

### setAccountName

Set the name for the account

```shell

USAGE
  $ ./marker setAccountName

OPTIONS
  --keystore                                                   Keystore file path of sender
                                                            
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --name                                                       name for the account
    
EXAMPLES:
./marker setAccountName --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d --name "so cool validator"

RESPONSE:
INFO [08-31|15:16:07.006] set name                                 address=0xeF021f15D188ad28625517A8D73CD20cE743a32D name="so cool validator"
INFO [08-31|15:16:09.039] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000d010 value=<nil> nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [08-31|15:16:09.448] Please waiting                           func=getResult                txHash =0x046ee71f161c06d34c4eb13ed9c536e909da152d9ca4036ccb240ae1ab23cb7f
INFO [08-31|15:16:10.983] Please waiting, Transaction is in pending status func=getResult
INFO [08-31|15:16:12.517] Please waiting, Transaction is in pending status func=getResult
INFO [08-31|15:16:14.048] Please waiting, Transaction is in pending status func=getResult
INFO [08-31|15:16:17.024] Transaction Success                      func=getResult               number=82133
```

### setNextCommissionUpdate

Queues an update to a validator's commission. If there was a previously scheduled update, that is overwritten.

`commission`: representation of the commission this validator receives on epoch payments made to its members. Must be in
the range [0, 1000000].

```shell

USAGE
  $ ./marker setNextCommissionUpdate

OPTIONS
  --keystore                                                   Keystore file path of sender
      
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                              
  --target                                                     target address that the account you want to query(including yourself)
    
EXAMPLES:
./marker setNextCommissionUpdate --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d --commission 300000

RESPONSE:
INFO [09-01|14:17:15.746] === setNextCommissionUpdate ===          commission=300,000
INFO [09-01|14:17:17.644] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000D012 value=<nil> nonce =14  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [09-01|14:17:18.062] Please waiting                           func=getResult                txHash =0xcb72b1a5055b290712a5f8e72a4bc60028b144ebea92c12637315891874ab8bf
INFO [09-01|14:17:20.911] Transaction Success                      func=getResult               number=98706
```

### updateCommission

Updates a validator's commission based on the previously queued update, The operation can only be executed after **2000**
blocks after the successful execution of [setNextCommissionUpdate](/develop/map-relay-chain/marker/AboutCommon.md#setnextcommissionupdate)

```shell

USAGE
  $ ./marker updateCommission

OPTIONS
  --keystore                                                   Keystore file path of sender
       
  --rpcaddr                                                    HTTP-RPC server address   
                                                                                                                                                                  
EXAMPLES:
./marker updateCommission --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-08-26T10-59-01.086763000Z--ef021f15d188ad28625517a8d73cd20ce743a32d

RESPONSE:
INFO [09-01|14:18:15.648] === updateCommission === 
INFO [09-01|14:18:17.303] Tx Info                                  func=sendContractTransaction from=0xeF021f15D188ad28625517A8D73CD20cE743a32D to=0x000000000000000000000000000000000000D012 value=<nil> nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =20212
INFO [09-01|14:18:17.717] Please waiting                           func=getResult                txHash =0x4f252120cc61e27d72d47e6f6a9dd732bb9aa319937b94fdb602c6c93feb4c83
INFO [09-01|14:18:20.536] Transaction Success                      func=getResult               number=98718
```