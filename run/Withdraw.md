## Validator withdraw

### Query the number of locked MAP

You can query how many MAP are locked by doing the following.

```shell
./marker getAccountTotalLockedGold --rpcaddr 127.0.0.1 --rpcport 7445 --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-02|16:50:22.331] === getAccountTotalLockedGold ===        admin=0x0000000000000000000000000000000000000000 target=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|16:50:22.333] result                                   lockedGold=1,000,000,000,000,000,000,000,000
```

### Unregister validator

This step is not required, but if your locked balance is less than 10000000 after unlocking, please unregister first.
unregister operation can only be performed 60 days after registration as a validator, and unregister needs to wait for
the last block of the current epoch to take effect.

```shell
./marker deregister --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --password ""

INFO [08-02|16:52:40.688] === deregisterValidator === 
INFO [08-02|16:52:40.701] TxInfo                                   func=sendContractTransaction TX data nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|16:52:40.702] Please waiting                           func=getResult                txHash =0xb904cae42c8e9d5481b0023b367a35a5be37802c1a9f7a9ff55835cd72044def
INFO [08-02|16:52:41.107] Transaction Success                      func=queryTx                 block Number=1211
```

### Unlock MAP

This step is used to convert the state of your MAP from locked to unlocked

```shell
./marker unlockMap --rpcaddr 127.0.0.1 --rpcport 7445 --keystore  /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --password "" --mapValue 0

INFO [08-02|17:24:16.166] === unLock validator gold === 
INFO [08-02|17:24:16.166] unLock validator gold                    amount=200,000,000,000,000,000,000,000 admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:16.180] TxInfo                                   func=sendContractTransaction TX data nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|17:24:16.181] Please waiting                           func=getResult                txHash =0x70b5c777daa507ca6b6e6f9132e757f8804c4147f0f8542f71a456ecf079bffa
INFO [08-02|17:24:21.229] Transaction Success                      func=queryTx                 block Number=1591
```

### Query the number of unlocked MAP

```shell
./marker getPendingWithdrawals --rpcaddr 127.0.0.1 --rpcport 7445 --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-02|17:24:28.336] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:28.343] result:                                  index=0 values=100,000,000,000,000,000,000,000 timestamps=1,660,727,111
INFO [08-02|17:24:28.343] result:                                  index=1 values=200,000,000,000,000,000,000,000 timestamps=1,660,728,211
```

Looking at the output, we can see that we now have two unlocked funds numbered 0 and 1. Next we will withdraw the fund
numbered 1.

### Withdraw MAP

This step will redeem the status of the reward from the unlocked state to the balance, but this step needs to be
unlocked for 15 days before it can be executed.

```shell
./marker withdrawMap --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-14T05-46-17.312327000Z--73bc690093b9dd0400c91886184a60cc127b2c33 --password "" --withdrawIndex 1

INFO [08-02|17:24:44.848] === withdraw validator gold ===          admin=0x73bC690093b9dD0400c91886184A60cC127b2c33
INFO [08-02|17:24:44.858] TxInfo                                   func=sendContractTransaction TX data nonce =16  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-02|17:24:44.859] Please waiting                           func=getResult                txHash =0x1d592838b4044a05072692ebc9e8ef68b3f14ff6faa690283f4f363bc6642639
INFO [08-02|17:24:46.072] Transaction Success                      func=queryTx                 block Number=1596
```

### Check balance

Now the redeemed MAP is in the account balance, let's verify it.

```shell
web3.fromWei(eth.getBalance("0x73bc690093b9dd0400c91886184a60cc127b2c33"), "ether")

200000.940633728411925694
```

## Voter withdraw

### Query the validators that account has voted for.

```shell
./marker getValidatorsVotedForByAccount --rpcaddr 127.0.0.1 --rpcport 7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f

INFO [08-03|14:41:27.096] === getValidatorsVotedForByAccount ===   admin=0x0000000000000000000000000000000000000000
INFO [08-03|14:41:27.100] validator                                Address=0x73bC690093b9dD0400c91886184A60cC127b2c33
```

### Query your account's active votes for validator

```shell
./marker getActiveVotesForValidatorByAccount --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --password "" --target 0x73bc690093b9dd0400c91886184a60cc127b2c33

INFO [08-03|14:41:59.009] === getActiveVotesForValidatorByAccount === admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:41:59.011] ActiveVotes                              balance=100,648,411,651,178,302,866,465
```

### Revokes active votes for validator

```shell
./marker revokeActive --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --password "" --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --mapValue 50000
INFO [08-03|14:43:01.909] === revokeActive ===                     admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:43:01.928] TxInfo                                   func=sendContractTransaction TX data nonce =5  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:43:01.931] Please waiting                           func=getResult                txHash =0x8dd81d60fdddb8e23f8edaeb243477bce34ab8e835d594f1081b17d4d48c089d
INFO [08-03|14:43:10.818] Transaction Success                      func=queryTx                 block Number=438
```

### Query the total amount of non-voting locked MAP in your account.

```shell
./marker getAccountNonvotingLockedGold --rpcaddr 127.0.0.1 --rpcport 7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f
INFO [08-03|14:43:20.659] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:43:20.661] result                                   lockedGold=50,000,000,000,000,000,000,000
```

### Unlock MAP

This step is used to convert the state of your MAP from locked to unlocked

```shell
./marker unlockMap --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --password "" --mapValue 50000
INFO [08-03|14:45:33.629] === unLock validator gold === 
INFO [08-03|14:45:33.629] unLock validator gold                    amount=50,000,000,000,000,000,000,000 admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:45:33.641] TxInfo                                   func=sendContractTransaction TX data nonce =6  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:45:33.642] Please waiting                           func=getResult                txHash =0xd23397b70eb3e13c90fa79c9c4e793569fb21a59bb80719a795864e6c5398b04
INFO [08-03|14:45:37.074] Transaction Success                      func=queryTx                 block Number=468
```

### Query the number of unlocked MAP

```shell
./marker getPendingWithdrawals --rpcaddr 127.0.0.1 --rpcport 7445 --target 0x6d842e9c25c0c6246231296ca6ecf4bc8268949f
INFO [08-03|14:45:57.066] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:45:57.069] result:                                  index=0 values=50,000,000,000,000,000,000,000 timestamps=1,660,805,137
```

Looking at the output, we can see that we now have a fund number 0 waiting to be redeemed, below we will redeem it

### Withdraw MAP

This step will redeem the status of the reward from the unlocked state to the balance, but this step needs to be
unlocked for 15 days before it can be executed.

```shell
./marker withdrawMap --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/data/atlas-1/keystore/UTC--2022-06-17T03-50-52.931374000Z--6d842e9c25c0c6246231296ca6ecf4bc8268949f --password "" --withdrawIndex 0

INFO [08-03|14:46:21.582] === withdraw validator gold ===          admin=0x6D842E9c25C0c6246231296ca6ECf4bC8268949F
INFO [08-03|14:46:21.591] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =22776
INFO [08-03|14:46:21.592] Please waiting                           func=getResult                txHash =0xdc8d99434cdb69ea22cd631b2768999126cf2c7ccc41cfb9fc8f2fabcf301e8c
INFO [08-03|14:46:25.229] Transaction Success                       func=queryTx                 Block Number=477
T
```

### Check balance

Now the redeemed MAP is in the account balance, let's verify it.

```shell
> web3.fromWei(eth.getBalance("0x6d842e9c25c0c6246231296ca6ecf4bc8268949f"), "ether")

50000.880957764
```