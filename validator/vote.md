## How To Vote

## Introduce

We can use our validator account to vote for ourselves, or we can let other validators or voters vote for ourselves.
Below we will demonstrate voting for ourselves using our own validator account, as this is the easiest.

The number of votes, and the number of MAP you pledge are 1 to 1

Before voting, you must first become a validator，If you are not a validator， please to see [this](/run/HowToBecomeANewValidator.md).

### Step 1: createAccount

If you have done this step before, please skip it.

In this step, you need to transfer your identification information to the corresponding management contract,which will manage your account, keys, and metadata.

The purpose of this step is keep your locked `MAP` more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked `MAP` in storage.

You need `createAccount` command to perform the above operations,more detail about `createAccount` command please to see [this](/develop/map-relay-chain/marker/AboutCommon.md#createAccount).

For example
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

### Step 2: locked `MAP`

If you have locked enough `MAP` for you to vote, please skip this step.

The purpose of this step is to deduct the part of the corresponding vote from the money you locked to the validator you want to vote,so you need to lock your `MAP` into the corresponding management contract in advance.

You need `lockedMAP` command to perform the above operations,more detail about `lockedMAP` command please to see [this](/develop/map-relay-chain/marker/AboutCommon.md#lockedMAP).

For example
```shell
./marker lockedMAP --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --lockedNum 1000000

INFO [07-01|06:12:45.068] === Lock  gold ===
INFO [07-01|06:12:45.069] Lock  gold                               amount=1000000000000000000000000
INFO [07-01|06:12:45.084] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-01|06:12:45.085] Please waiting                           func=getResult                txHash =0x6f8c9ea1d4481ba26af144d4cf04b5275750f51b899a898e6c57a798fdbcceab
INFO [07-01|06:12:47.095] Transaction Success                      func=queryTx                 block Number=360,302
```

### Step 3: vote

This step is voting.

When you get to this step, you can vote for your favorite validator. You can use the `getTotalVotesForEligibleValidators` subcommand to view all current validators and their votes

For example
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

For `vote` example
```shell
./marker vote --rpcaddr http://127.0.0.1:7445 --keystore ./UTC--2022-07-01T04-02-22.985282926Z--078f684c7d3bf78bdbe8bef93e56998442dc8099 --target "0x078F684c7d3bf78BDbe8bEf93E56998442dc8099" --voteNum 100000

INFO [07-06|06:05:31.454] === vote Validator ===                   admin=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 voteTargetValidator=0x078F684c7d3bf78BDbe8bEf93E56998442dc8099 vote MAP Num=100000
INFO [07-06|06:05:31.478] TxInfo                                   func=sendContractTransaction TX data nonce =10  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =29088
INFO [07-06|06:05:31.479] Please waiting                           func=getResult                txHash =0x8f8724be7e7eb2ffb0034dee92dbe57fbd2534b057ba43adcc7c6be5b20b9ffa
INFO [07-06|06:05:32.083] Transaction Success                      func=queryTx                 block Number=446,614
```

When you finish voting,your votes will be in the status of pending. At the end of epoch block, the Elected validator will automatic activation the votes in pending related to him, you can also use `active` command to activate your votes yourself. this active operation need to be greater than your pending vote epoch.

You can click the [active](./marker/AboutVote.md#activate) and [vote](/develop/map-relay-chain/marker/AboutVote.md#vote)  to see detail.

## Expand:

### Case: When you vote for a validator who is not yet been elected.

your pending votes will not be automatic activation,until validator is elected , validator will automatic activation your votes at the next epoch.
so If you don't activate your vote, your votes will get benefits after the second epoch when validator be elected.


