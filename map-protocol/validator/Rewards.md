# Epoch Reward

Introduction to Makalu chain epoch rewards and the target reward release schedule.

## What are Epoch Rewards?

Epoch Rewards are similar to the familiar notion of block rewards in other blockchains, minting and distributing new
units of MAP as blocks are produced, to create several kinds of incentives.

## Epoch rewards are paid in the final block of the epoch and are used to:

1.Distributed rewards for validators

2.Distribute rewards to holders of Locked MAP voting for validator that elected validators

3.Make payments into a Community Fund for protocol infrastructure grants

Each epoch will be rewarded with a fixed reward

- `EpochReward`= 1,000,000 `MAP`.

## Reward Disbursement

The amount of disbursements is determined at the end of every epoch via a three step process.

### Step 1

In step one，Community Fund will receive a fixed percentage of the reward

- `CommunityFundReward`  =  `CommunityFundMultiplier`*`EpochReward`
- `CommunityFundMultiplier` defult value = 0.1.

### Step 2

In step two,we will Count the number of signatures of each validator and convert it into a score(`Sorce`>=0,<=1), which
will eventually participate in the calculation of rewards.

If the validator fails to fulfill its responsibilities, it will be punished through the punishment mechanism.

So per validator will receive the reward:

- `ValidatorReceived` = `(`EpochReward`-`CommunityFundReward`)` \* `Punishment` \* (`P` +` mySorce`) / (`N` * `P` + `Sorce1` + `Score2` +...`ScoreN`).
- `P`  A fixed reward proportion shared by the validator(p>0, p<=1, default value is 1)
- `N` Number of validator
- `Punishment`  Punishment mechanism param.

The score is calculated by this formula:

- `NewScore` = `UptimeScore` \* `AdjustmentSpeed`+`OldScore` \* `(1 - `AdjustmentSpeed`)`
- `UptimeScore` This is score of work done at `TotalMonitoredBlocks`.

  - The `TotalMonitoredBlocks` are the total number of block on which we monitor uptime for the epoch
  - `TotalMonitoredBlocks` value range is  `[EpochFirstBlock + lookbackWindowSize(defult = 12) -1,EpochLastBlock - BlocksToSkipAtEpochEnd(defult = 2)]`
  - `lookbackWindowSize` A fixed value about lookbackWindow check whether the validator has signed in a fixed interval.
  - If you have a successful sign in `[NowBlockNumber-lookbackWindow ,NowBlockNumber]`, we will regard your current block as a successful work.
  - `BlocksToSkipAtEpochEnd` represents the number of blocks to skip on the monitoring window from the end of the
    epoch
    - About currently we skip blocks:
    - lastBlock => its parentSeal is on firstBlock of next epoch
    - lastBlock - 1 => parentSeal is on lastBlockOfEpoch, but validatorScore is computed with lastBlockOfEpoch and
      before updating scores
    - (lastBlock-1 could be counted, but much harder to implement)
  - About first block to monitor:
    - we can't monitor uptime when current lookbackWindow crosses the epoch boundary
    - thus, first block to monitor is the final block of the lookbackwindow that starts at firstBlockOfEpoch
  - So `UptimeScore` = `SignedBlocks ` / `TotalMonitoredBlocks`.
    - `SignedBlocks ` Number of blocks signed by validator,this will be checked every time the block is produced.
    - First, check whether the current block is in `BlocksToSkipAtEpochEnd`.
    - Second, check whether the current block review period is signed. If it exists, the signature will be regarded
      as successful
    - if First step and Second step is successful `SignedBlocks` will add 1.
- `AdjustmentSpeed`  A adjustment factor.This way will encourage the validator to stabilize.('AdjustmentSpeed' default value is 1).
- `OldScore`. This is the score of the last epoch of this validator.(In the fist distribute epoch reward OldScore is 0)

### Step 3

In step three, because the validator will distribute the reward to voter, the actual validator will receive the reward:

- `ValidatorActualReceived` =`ValidatorReceived`*`Commission`.
- `Commission` A proportional value drawn by the validator in proportion to the `ValidatorReceived`.

The validator's voters will receive:

-`VotersReward` = `ValidatorReceived` - `ValidatorActualReceived`.

These rewards(`VotersReward`) will be returned to the voting pool. This operation is equivalent to an increase in the number of votes per voter. Voters draw their votes from the voting pool and get corresponding rewards.

Per voter's reward will be obtained according to the voting proportion of voter to validator.

## Example:

epoch size = 100, The award for each epoch size is 1000,000 `MAP` ,There are four validators V1、V2、V3、V4 and a community fund C1.

The Commission of V1 registration is 0.1, and he voted 100,000 `MAP` for himself.

The Commission of V2 registration is 0.2, and he voted 100,000 `MAP` for himself and 100,000 `MAP` for V1.

The Commission of V3 registration is 0.5, and he voted 200,000 `MAP` for himself.

The Commission of V4 registration is 0.6, and he voted 300,000 `MAP` for himself and 300,000 `MAP` for V3.

If every validator has done a good job in this epoch. so each validator will get all the scores.

Reward validator:

Reward for C1:1000,000 \* 0.1 = 100,000 `MAP`

Because they have the same score, they get the same reward (1000,000 - 100,000) \* (1 + 1)/(1*4 + 1 + 1 + 1 + 1) = 225,000 `MAP`

Reward for V1: 225,000 \*0.1 = 22,500 `MAP`

Reward for V2: 225,000 \*0.2 = 45,000 `MAP`

Reward for V3: 225,000 \*0.5 = 112,500 `MAP`

Reward for V4: 225,000 \*0.6 = 135,000 `MAP`

Reward voter:

Reward for V1 voters:

Reward for V1: 225,000 \*0.9 \* ( 100,000 / (100,000 + 100,000) )  = 101,250 `MAP`

Reward for V2: 225,000 \*0.9 \* ( 100,000 / (100,000 + 100,000) )  = 101,250 `MAP`

Reward for V2 voters:

Reward for V2: 225,000 \*0.8 \* ( 100,000 / 100,000 )  = 180,000 `MAP`

Reward for V3 voters:

Reward for V3: 225,000 \*0.5 \* ( 200,000 / (200,000 + 300,000) )  = 45,000 `MAP`

Reward for V4: 225,000 \*0.5 \* ( 300,000 / (200,000 + 300,000) )  = 67,500 `MAP`

Reward for V4 voters:

Reward for V4: 225,000 \*0.4 \* ( 300,000 / 300,000 )  = 90,000 `MAP`

## Expand:

### case One: Validator was deRegister during his service.

In this case,we won't consider him when awarded,the rest validators will share his reward equally.

### case Two: Validator Re register.

When a validator is deRegistered, voters can still cancel the votes on the deRegistered validator as they wish.

If voter don't withdraw his vote,voters vote will remain in the voting area,Wait for the validator to register again,
and these tickets will be returned to the validator again.
