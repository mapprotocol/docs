## How to become a new validator

In order to make your assets safer, we need you to set some necessary identification parameters to become a validator. We also set the corresponding threshold so that we can screen those who really want to contribute to the chain. Of course, we will give corresponding rewards to these people.

The following steps are regarded as your first operation, because you only need to perform the following operations once to become a validator. Unless you log off the validator or cancel the corresponding operation, you will not need to perform the second operation to avoid wasting your gas fee.

### Step 1: create Account

In this step, you need to transfer your identification information to the corresponding management contract,which will manage your account, keys, and metadata.

The purpose of this step is keep your locked `MAP` more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked `MAP` in storage.

You need `createAccount` command to perform the above operations,more detail about `createAccount` command please to see [this](../../marker/AboutCommon.md#CreateAccount).

### Step 2: locked `MAP`

The threshold we set to be the validator is to lock 1000,000 `MAP` into the corresponding management smart contract.

This part of the locked `MAP` will be used for future punishment, which is also one of the conditions for being elected.

You need `createAccount`  command to perform the above operations,more detail about `lockedMAP` command please to see [this](../../marker/AboutCommon.md#LockedMAP) .

### Step 3: validator register

This step is a key step for registering as a new validator.

You need `register`  command to perform the above operations,more detail about `register` command please to see [this](../../marker/AboutValidator.md#Register).

At this step, you will successfully register as a validator. Next, you can try to vote for yourself. How to vote please to see[this](../../marker/AboutVote.md#Vote).



## Example
If you want to become a validator, you need to meet two conditions:

1. A node that provides rpc services.
2. You need to have at least 1,000,000 MAP, you can modify the generated genesis.json file to set the balance for your account, then use the modified genesis file to initialize the node.

Below are the links to the required actions:

- [Start an RPC node ](../../../map-protocol/privatenet/PrivateNet.md#start-four-validator-nodes)
- [Generating a genesis.json](../../../map-protocol/privatenet/PrivateNet.md#generating-a-genesis.json)
- [Initialize validator node](../../../map-protocol/privatenet/PrivateNet.md#initialize-four-validator-nodes)


Set 2000000 MAP for 0xba509ea628900bf10639c7591467004514a54600:

```json
{
  ...
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
    "ba509ea628900bf10639c7591467004514a54600": {
      "balance": "0x1a784379d99db42000000"
    },
    ...
  },
  ...
}
```

### Create account
```shell
./marker createAccount --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T05-56-33.162678000Z--ba509ea628900bf10639c7591467004514a54600 --password "" --namePrefix "validator"

# Output

```

### Locked MAP
```shell
./marker lockedMAP --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T05-56-33.162678000Z--ba509ea628900bf10639c7591467004514a54600 --password "" --lockedNum 1000000

# Output

```

### Validator register
```shell
./marker register --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T05-56-33.162678000Z--ba509ea628900bf10639c7591467004514a54600 --password "" --commission 0.6

# Output

```

### Verify
So far we have completed the registration steps of validator, now let's verify whether it has become a validator.

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T05-56-33.162678000Z--ba509ea628900bf10639c7591467004514a54600 --password ""

# Output

```
From the above results, we can see that we have become a validator.
