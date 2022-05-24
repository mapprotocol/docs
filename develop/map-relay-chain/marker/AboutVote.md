## About Vote

Introduction about vote commands.

### Vote

You must lock enough `MAP` into the `lockedGold` contract in advance and register your information into the contract.

Decrements the number of total and  the number of nonvoting  corresponding to your previously registered account in the `LockedGold` contract.

Increments the number of total and pending votes for validator in the `Election` contract.

```shell
USAGE
  $ ./Marker vote

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port
                                                               
  --validator                                                  Your target validator address
  
  --voteNum                                                    Your number of votes

                                                                                                            
EXAMPLES:
./Marker vote
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--validator "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000


RESPONSE:
success
or
Failed
```


### quicklyVote

If you have not creat Account or locked the Map, you can quickly vote through the `quicklyVote` command, which
integrates the `createAccount` `lockedMAP`

Please note that you can only use this command once. No matter whether the command succeeds or fails, this command only packs and combines `createAccount` `lockedMAP` commands and does not have the nature of reuse.

```shell
USAGE
$ ./Marker  quicklyVote   

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port
                                                               
  --validator                                                  Your target validator address
  
  --voteNum                                                    Your number of votes
  
  --lockedNum                                                  In order to vote to validator,
                                                               you will lock enough `MAP`.

                                                                                                            
EXAMPLES:
./Marker vote
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--validator "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000
--lockedNum 10000


RESPONSE:
success
or
Failed
```

### Activate

Activate pending votes in validator elections to begin earning rewards. To earn rewards as a voter, it is required to activate your pending votes at some point after the end of the epoch in which they were made.

That means converts `account`'s pending votes for `validator` to active votes.

```shell
USAGE
$ ./Marker  activate   

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port
                                                               
  --validator                                                  Your target validator address
                                                               you can query it from 
                                                               `getTotalVotesForEligibleValidators`
                                                               command
  


                                                                                                            
EXAMPLES:
./Marker vote
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--validator "0x81f02fd21657df80783755874a92c996749777bf"

RESPONSE:
success
or
Failed
```

### RevokePending

Revokes `value` pending votes for `validator`.

This command will put the voting `MAP` turn into nonvoting `MAP`.

Increments the number of total and the number of nonvoting  corresponding to your previously registered account in the `LockedGold` contract.

Decrements the number of total and pending votes for validator in the `Election` contract.


```shell
USAGE
$ ./Marker  revokePending   

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port
                                                               
  --validator                                                  Your target validator address
                                                               you can query it from 
                                                               `getTotalVotesForEligibleValidators`
                                                               command
                                                               
  --mapValue                                                   The `MAP` that you want to revoke from your penging votes for validator
  


                                                                                                            
EXAMPLES::
./Marker revokePending
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--validator "0x81f02fd21657df80783755874a92c996749777bf"

RESPONSE:
success
or
Failed
```

### RevokeActive

Revokes `value` active votes for `validator`

This command will put the voting `MAP` turn into nonvoting `MAP`.

Increments the number of total and the number of nonvoting corresponding to your previously registered account in the `LockedGold` contract.

Decrements the number of total and active votes for validator in the `Election` contract.

```shell
USAGE
$ ./Marker  revokeActive

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port
                                                               
  --validator                                                  Your target validator address
                                                               you can query it from 
                                                               `getTotalVotesForEligibleValidators`
                                                               command
                                                               
  --mapValue                                                   The `MAP` that you want to revoke from your active votes for validator
                                                                                                             
EXAMPLES:
./Marker revokeActive
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--validator "0x81f02fd21657df80783755874a92c996749777bf"

RESPONSE:
success
or
Failed
```

