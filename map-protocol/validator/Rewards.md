# Epoch Reward

Introduction to Map chain epoch rewards and the target reward release schedule.

## What are Epoch Rewards?

Epoch Rewards are similar to the familiar notion of block rewards in other blockchains, minting and distributing new
units of MAP as blocks are produced, to create several kinds of incentives.

## Epoch rewards are paid in the final block of the epoch and are used to:

1. Distributed rewards for validators

2. Distribute rewards to holders of Locked MAP voting for validator that elected validators

3. Make payments into a Community Fund for protocol infrastructure grants

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
  
- `P`   A fixed reward proportion shared by the validator(p>0, p<=1, default value is 1)
  
- `N`  Number of validator
  
- `Punishment`  Punishment mechanism param.

The score is calculated by this formula:

- `NewScore` = `UptimeScore` \* `AdjustmentSpeed`+`OldScore` \* `(1 - `AdjustmentSpeed`)`
  
- `UptimeScore` This is score of work done at `TotalMonitoredBlocks`.

  - The `TotalMonitoredBlocks` are the total number of block on which we monitor uptime for the epoch.
  
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
  
    - we can't monitor uptime when current lookbackWindow crosses the epoch boundary.
  
    - thus, first block to monitor is the final block of the lookbackwindow that starts at firstBlockOfEpoch.
  
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

the initial parameters of atlas:
```
epoch size = 100

reward of epoch = 1,000,000

four validators for consensus(V1、V2、V3、V4) with fee rate (
  0.1,0.2,0.5,0.6) and all votes: (200,000,100,000,500,000,300,000)

// V1 validator has two voters(V1 + V2)
V1's vote = 100,000(self vote) + 100,000(V2 vote) = 200,000

// V2 validator has one voter (V2)
V2's vote = 100,000(self vote) = 100,000

// V3 validator has two voters(V3 + V4)
V3's vote = 200,000(self vote) + 300,000(V4 vote) = 500,000

// V4 validator has one voter (V4)
V4's vote = 300,000(self vote) = 300,000

community fund of C1
```

If every validator has done a good job in the epoch. so each validator will get all the scores.

Calculate the reward in end of the epoch:
```
C1 = 1,000,000 * 10% = 100,000

// refor to reward = (EpochReward-CommunityFundReward) * Punishment * (P + mySorce) / (N * P + Sorce1 + Score2 +...ScoreN)
// validator = reward * fee rate
// four validators have same score: 1 (0 <= score <= 1)

V1 = (1,000,000-C1) * (1+1) / (1*4 + 1 + 1 + 1 + 1) * 0.1 = 22,500
V2 = (1,000,000-C1) * (1+1) / (1*4 + 1 + 1 + 1 + 1) * 0.2 = 45,000
V3 = (1,000,000-C1) * (1+1) / (1*4 + 1 + 1 + 1 + 1) * 0.5 = 112,500
V4 = (1,000,000-C1) * (1+1) / (1*4 + 1 + 1 + 1 + 1) * 0.6 = 135,000
```

Calculate the reward for voter:

```
// V1 validator:
V1 voter = 225,000 * (1-0.1) * (100,000 / 200,000) = 101,250
V2 voter = 225,000 * (1-0.1) * (100,000 / 200,000) = 101,250

// V2 validator:
V2 voter = 225,000 * (1-0.2) * (100,000 / 100,000) = 180,000

// V3 validator:
V3 voter = 225,000 * (1-0.5) * (200,000 / 500,000) = 45,000
V4 voter = 225,000 * (1-0.5) * (300,000 / 500,000) = 67,500

// V4 validator:
V4 voter = 225,000 * (1-0.6) * (300,000 / 300,000) = 90,000

```

## Expand:

### case One: Validator was deRegister during his service.

In this case,we won't consider him when awarded,the rest validators will share his reward equally.

### case Two: Validator Re register.

When a validator is deRegistered, voters can still cancel the votes on the deRegistered validator as they wish.

If voter don't withdraw his vote,voters vote will remain in the voting area,Wait for the validator to register again,
and these tickets will be returned to the validator again.
