## How To withdraw Locked MAP

### Step 1: unlock

Map chain implements an unlocking period, a delay of 3 days after making a request to unlock Locked `MAP` before it can be recovered from the escrow.

Unlocks `MAP`, which can be withdrawn after the unlocking period. Unlocked `MAP` will appear as a "pending withdrawal" until the unlocking period is over, after which it can be withdrawn via "withdraw" command.

You can only unlock your nonvoting `MAP`,if you want to cancel your voting `MAP` please to Reference [How To withdraw voting MAP](../Overview/HowToWithdraw.md#how-To-withdraw-voting-MAP)

If you are a validator you will not be able to unlock 1000,000 `MAP` that necessary pledge as validator.

You need `unlock` command to perform unlock operations,more detail about `unlock` command please to see [this](../../marker/AboutCommon.md#unlockMAP) .

### Step 2:withdraw

Withdraws `MAP` that has been unlocked after the unlocking period has passed. this step equivalent to withdrawing the money you locked in the smart contract to your account address.

You need `withdraw` command to perform withdraw operations,more detail about `withdraw` command please to see [this](../../marker/AboutCommon.md#withdrawMap) .

At this step, the `MAP` you locked has been transferred to your account address. You can inquire by yourself.

## How To withdraw voting MAP

### Step 1:

revokePending:

Because your current vote is to contribute to the next validator, so your voting `MAP` will go through two states not active state and active state.

You can use `getPendingVotesForValidatorByAccount` command to  check whether you have inactive `MAP`

If your voting `MAP` is not active, you can use 'revokePending' command to withdraw your not active voting `MAP`.

revokeActive:

If your voting `MAP` is active, you can use `revokeActive` command to withdraw your  active voting `MAP`. If you perform this step, the votes you withdraw will lose the corresponding reward.

Both of `revokePending` and `revokeActive` command will put the voting `MAP` turn into nonvoting `MAP` 

Here are the details of these two commands please click [revokePending](../../marker/AboutVote.md#revokePending) or [revokeActive](../../marker/AboutVote.md#revokeActive) to see.

### Step 2:

Now your voting `MAP` state has changed from not active state or active state to locking state.

the next step is same to [How To withdraw Locked MAP](../Overview/HowToWithdraw.md#how-To-withdraw-Locked-MAP)
