## How To withdraw voting MAP

### Introduce

When you use the vote command to vote, you're voting status is pending. Only when the last block of the current epoch is generated, your voting status will automatically change to active。
Next, follow this document, and you will withdraw your vote

#### How To withdraw pending vote

Because your current vote is to contribute to the `validator`(in the next epoch), so your voting `MAP` will go through two states the `pending` and the `active` state.

So， You can use `getPendingVotesForValidatorByAccount` command to  check whether you have inactive `MAP`

For Example
```shell
./marker getPendingVotesForValidatorByAccount --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445

INFO [07-07|13:32:03.891] === getPendingVotesForValidatorByAccount === admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-07|13:32:03.894] PendingVotes                             balance=100,000,000,000,000,000,000,000
```

If your voting `MAP` is in `pending`, you can use 'revokePending' command to withdraw your pending voting `MAP`.

For Example
```shell
./marker revokePending --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445 --lockedNum 100000 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099

INFO [07-08|02:39:49.006] === revokePending ===                    admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|02:39:49.028] TxInfo                                   func=sendContractTransaction TX data nonce =14  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|02:39:49.029] Please waiting                           func=getResult                txHash =0x9b0c2bedf7a0fe1b29f698ac069c6f549073241bb08932f9592132908596989b
INFO [07-08|02:39:50.839] Transaction Success                      func=queryTx                 block Number=478,705
```

#### How To withdraw active vote

If you're voting `MAP` is in `active`, you can use `revokeActive` command to withdraw your  active voting `MAP`. If you perform this step, the votes you withdraw will lose the corresponding reward.

First, you can use the `getActiveVotesForValidatorByAccount` command to see how many active votes you have

For Example
```shell
./marker getActiveVotesForValidatorByAccount --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099

INFO [07-08|03:45:18.228] === getActiveVotesForValidatorByAccount === admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|03:45:18.230] ActiveVotes                              balance=304,805,454,545,461,535,206,581
```

withdraw active vote

For Example 
```shell
./marker revokeActive --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --rpcaddr http://127.0.0.1:7445 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 --lockedNum  100000

INFO [07-08|05:47:48.065] === revokeActive ===                     admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|05:47:48.088] TxInfo                                   func=sendContractTransaction TX data nonce =15  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|05:47:48.089] Please waiting                           func=getResult                txHash =0x24e55381f755affdc1e6916b8dbc6fa431623181490731dc77e18e01b26db0df
INFO [07-08|05:47:51.709] Transaction Success                      func=queryTx                 block Number=480,961
```

Then you can also use the `getActiveVotesForValidatorByAccount` command to confirm whether the revoke is successful

> Here are the details of these two commands please click [revokePending](/develop/map-relay-chain/marker/AboutVote.md#revokepending) or [revokeActive](/develop/map-relay-chain/marker/AboutVote.md#revokeactive) to see.

> be careful ： Both of `revokePending` and `revokeActive` command will put the voting `MAP` turn into nonvoting `MAP`
> 
> Now your voting `MAP` state has changed from pending state or active state to locking state.
> 
> the next step is same to [How To withdraw Locked MAP](/develop/map-relay-chain/how-to-withdraw.md#how-to-withdraw-locked-map)


## How To withdraw Locked MAP

### Step 1: unlock

You can use the `getAccountNonvotingLockedGold` command to see how many nonvoting maps you have

For Example
```shell
/marker getAccountNonvotingLockedGold --rpcaddr http://127.0.0.1:7445 --target 0x078F684c7d3bf78BDbe8bEf93E56998442dc8099

INFO [07-08|06:13:47.791] === getAccountNonvotingLockedGold ===    admin=0x0000000000000000000000000000000000000000 target=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|06:13:47.793] result                                   lockedGold=800,000,000,000,000,000,000,000
```

Map chain implements an unlocking period, a delay of 60 days after making a request to unlock Locked `MAP` before it can be recovered from the escrow.

Unlocks `MAP`, which can be withdrawn after the unlocking period. Unlocked `MAP` will appear as a "pending withdrawal" until the unlocking period is over, after which it can be withdrawn via "withdraw" command.

You can only unlock your nonvoting `MAP`,if you want to cancel your voting `MAP` please to Reference [How To withdraw voting MAP](/develop/map-relay-chain/how-to-withdraw.md#how-to-withdraw-voting-map)

If you are a validator you will not be able to unlock 1000,000 `MAP` that necessary pledge as validator.

You need `unlock` command to perform unlock operations,more detail about `unlock` command please to see [this](/develop/map-relay-chain/marker/AboutCommon.md#unlockmap) .

For Example
```shell
./marker unlockMap --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --lockedNum 100000

INFO [07-08|06:30:08.880] === unLock validator gold ===
INFO [07-08|06:30:08.880] unLock validator gold                    amount=1,000,000,000,000,000,000,000 admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|06:30:08.903] TxInfo                                   func=sendContractTransaction TX data nonce =19  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|06:30:08.904] Please waiting                           func=getResult                txHash =0x42fca349f68c7018577f9f78e7b7560e12954c273bbb3fd1fe1cf88f127f2b01
INFO [07-08|06:30:11.117] Transaction Success                      func=queryTx                 block Number=481,469
```

You can use `getPendingWithdrawals` command to view the map that can be withdrawn at present

For Example
```shell
./cmd/marker/marker getPendingWithdrawals --rpcaddr http://127.0.0.1:7445 --target 0x19b375ebb9ee1b21a592a933c6383df49380046d

INFO [07-08|06:25:27.544] === getPendingWithdrawals ===            admin=0x0000000000000000000000000000000000000000 target=0x19b375EBB9eE1B21A592A933c6383Df49380046D
INFO [07-08|06:25:27.546] result:                                  index=0 values=10,000,000,000,000,000,000,000 timestamps=1,658,286,912
```

**Timestamps is the time you can withdraw, in seconds**

### Step 2:withdraw

Withdraws `MAP` that has been unlocked after the unlocking period has passed. this step equivalent to withdrawing the money you locked in the smart contract to your account address.

You need `withdraw` command to perform withdraw operations,more detail about `withdraw` command please to see [this](/develop/map-relay-chain/marker/AboutCommon.md#withdrawmap) .

At this step, the `MAP` you locked has been transferred to your account address. You can inquire by yourself.

For Example
```shell
./marker withdrawMap --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --withdrawIndex 0

INFO [07-08|06:32:04.394] === withdraw validator gold ===          admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099
INFO [07-08|06:32:04.406] TxInfo                                   func=sendContractTransaction TX data nonce =20  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-08|06:32:04.408] Please waiting                           func=getResult                txHash =0x4d874ff5792585ed91ad98379bf5243ff3383218a43967c3b9c5d1d633dc2340
INFO [07-08|06:32:06.218] Transaction Success                      func=queryTx                 block Number=481,492
```

After withdraw, you can check your balance， Map recommends that you use RPC interface.

For Example
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
