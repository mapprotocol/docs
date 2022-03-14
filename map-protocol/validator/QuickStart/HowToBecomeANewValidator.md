## How to become a new validator

In order to make your assets safer, we need you to set some necessary identification parameters to become a validator. We also set the corresponding threshold so that we can screen those who really want to contribute to the chain. Of course, we will give corresponding rewards to these people.

The following steps are regarded as your first operation, because you only need to perform the following operations once to become a validator. Unless you log off the validator or cancel the corresponding operation, you will not need to perform the second operation to avoid wasting your gas fee.

### Step 1: create Account

In this step, you need to transfer your identification information to the corresponding management contract,which will manage your account, keys, and metadata.

The purpose of this step is keep your locked `MAP` more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked `MAP` in storage.

You need `createAccount` command to perform the above operations,more detail about `createAccount` command please to see [this](../Marker/AboutCommon.md#CreateAccount).

### Step 2: locked `MAP`

The threshold we set to be the validator is to lock 1000,000 `MAP` into the corresponding management smart contract.

This part of the locked `MAP` will be used for future punishment, which is also one of the conditions for being elected.

You need `createAccount`  command to perform the above operations,more detail about `lockedMAP` command please to see [this](../Marker/AboutCommon.md#LockedMAP) .

### Step 3: validator register

This step is a key step for registering as a new validator.

You need `register`  command to perform the above operations,more detail about `register` command please to see [this](../Marker/AboutValidator.md#Register).

At this step, you will successfully register as a validator. Next, you can try to vote for yourself. How to vote please to see[this](../Marker/AboutVote.md#Vote).
