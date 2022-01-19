# Epoch Reward

Introduction to Atlas epoch rewards and the target reward release schedule.

## What are Epoch Rewards?

Epoch Rewards are similar to the familiar notion of block rewards in other blockchains, minting and distributing new units of MAP as blocks are produced, to create several kinds of incentives.

## Epoch rewards are paid in the final block of the epoch and are used to:

1.Distributed rewards for validators

2.Distribute rewards to holders of Locked MAP voting for validator that elected validators

3.Make payments into a Community Fund for protocol infrastructure grants

Each epoch will be rewarded with a fixed reward (`epochReward` default value is 160k MAP).

## Reward Disbursement

The amount of disbursements is determined at the end of every epoch via a three step process.

### Step 1

In step one，Community Fund  will receive a fixed percentage of the reward(`communityFundReward` = `CommunityFundMultiplier`*`epochReward` ,`CommunityFundMultiplier` defult value = 0.5).

### Step 2

In step two,we will Count the number of signatures of each validator and convert it into a score(`Sorce` >=0,<=1), which will eventually participate in the calculation of rewards.

If the validator fails to fulfill its responsibilities, it will be punished through the punishment mechanism.

So per validator will receive the reward('validatorReceived') = (`epochReward` - `communityFundReward`) * `myScore` * `punishment` * (`P` +` mySorce`) / (`N` * `P` + `Sorce1` + `Score2` +...`ScoreN`).

`P` is the fixed reward proportion shared by the validator(p>0, p<=1, default value is 1)

`punishment` is the punishment mechanism param.

New score is calculated by this formula: "`new_score` = `uptime`  * `adjustmentSpeed` + `old_score` * (1 - `adjustmentSpeed`)"

`old_score` This is the score of the last epoch of this validator.（when the fist distribute epoch reward old_score is 0）

`adjustmentSpeed` .This is an adjustment factor.This way will encourage the validator to stabilize.('adjustmentSpeed' default value is 0.1).

### Step 3

In step three, because the validator will distribute the reward to voter, the actual validator will receive the reward(`validatorActualReceived`) =

`validatorReceived` * `Commission`.

`Commission` is the proportional value drawn by the validator in proportion to the `validatorReceived`.

The validator's voters will receive `validatorReceived` * (1-`Commission`).

Per voter's reward will be obtained according to the voting proportion of voter to validator
