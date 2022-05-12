## How To Vote

### Step 1: createAccount

If you have done this step before, please skip it.

In this step, you need to transfer your identification information to the corresponding management contract,which will manage your account, keys, and metadata.

The purpose of this step is keep your locked `MAP` more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked `MAP` in storage.

You need `createAccount` command to perform the above operations,more detail about `createAccount` command please to see [this](../../marker/AboutCommon.md#createAccount).

### Step 2: locked `MAP`

If you have locked enough `MAP` for you to vote, please skip this step.

The purpose of this step is to deduct the part of the corresponding vote from the money you locked to the validator you want to vote,so you need to lock your `MAP` into the corresponding management contract in advance.

You need `lockedMAP` command to perform the above operations,more detail about `lockedMAP` command please to see [this](../../marker/AboutCommon.md#lockedMAP).

### Step 3: vote

This step is voting.

When you get to this step, you can vote for your favorite validator.

When you finish voting,your votes will be in the status of pending. At the end of epoch block, the Elected validator will automatic activation the votes in pending related to him, you can also use `active` command to activate your votes yourself. this active operation need to be greater than your pending vote epoch.

You can click the [active](../../marker/AboutVote.md#activate) and [vote](../../marker/AboutVote.md#vote)  to see detail.

## Expand:

### Case: When you vote for a validator who is not yet been elected.

your pending votes will not be automatic activation,until validator is elected , validator will automatic activation your votes at the next epoch.
so If you don't activate your vote, your votes will get benefits after the second epoch when validator be elected.


