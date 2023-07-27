# Locked MAP And Voting

If you create Account and locked `MAP` enough, you can vote as a voter.Note that the minimum locking amount is 10 `MAP`.

If your account has become a validator, you can also use this account as a voter to vote.

Introduction to locked `MAP` and how to  participate in voting.

## Validator Election Participation

To participate in validator elections, users must first make a transfer of `MAP` to the `LockedGold` smart contract.

## Concurrent Use of Locked MAP

Locking up `MAP` guarantees that the same asset is not used more than once in the same vote.

## Unlocking Period

Map chain implements an unlocking period, a delay of 15 days after making a request to unlock Locked `MAP` before it can be recovered from the escrow.

This value balances two concerns. First, it is long enough that an election will have taken place since the request to unlock, so that those units of `MAP` will no longer have any impact on which validators are managing the network. This deters an attacker from manipulations in the form of borrowing funds to purchase `MAP`, then using it to elect malicious validators, since they will not be able to return the borrowed funds until after the attack, when presumably it would have been detected and the borrowed funds’ value have fallen.

Second, the unlocking period is short enough that it does not represent a significant liquidity risk for most users. This limits the attractiveness to users of exchanges creating secondary markets in Locked `MAP` and thereby pooling voting power.

## Locking and Voting Flow

The flow is as follows:

- An account calls `lock`, transferring an amount of `MAP` from their balance to the `LockedGold` smart contract. This increments the account's 'non-voting' balance by the same amount.
- Then the account calls `vote`, passing in an amount and the address of the validator to vote for. This decrements the account's 'non-voting' balance and increments the 'pending' balance associated with that validator by the same amount. This counts immediately towards electing validators. Note that the vote may be rejected if it would mean that the account would be voting for more than 10 distinct validators.
- At the end of the current epoch, the protocol will first deliver epoch rewards to validators and voters based on the current epoch (pending votes do not count for these purposes), and then run an election to select the active validator set for the following epoch.
- The pending vote continues to contribute towards electing validators until it is changed, but the account must call activate (in a subsequent epoch to the one in which the vote was made) to convert the pending vote to one that earns rewards.(If you do not activate your vote, we will automatically activate your inactive vote)
- At the end of that epoch, if the validator for which the vote was made had elected one or more validators in the prior election, then the activated vote is eligible for Locked `MAP` rewards. These are applied to the pool of activated votes for the validator. This means that activated voting Locked `MAP` automatically compounds, with the rewards increasing the account's votes for the same validator, thereby increasing future rewards, benefitting participants who have elected to continuously participate in governance.
- The account may subsequently choose to `unvote` a specific amount of voting Locked `MAP` from a validator, up to the total balance that the account has accrued there. Due to rewards, this Locked `MAP` amount may be higher than the original value passed to vote.
- This Locked `MAP` immediately becomes non-voting, receives no further Epoch Rewards, and can be re-used to vote for a different validator.
- The account may choose to `unlock` an amount of Locked `MAP` at any time, provided that it is inactive: this means it is non-voting in Validator Elections, the `deregistrationPeriod` has elapsed if the amount has been used as a validator stake. Once an unlocking period of 3 days has passed, the account can call withdraw to have the LockedGold contract transfer them that amount.

Votes persist between epochs, and the same vote is applied to each election unless and until it is changed. Vote withdrawal, vote changes, and additional `MAP` being used to vote have no effect on the validator set until the election finalizes at the end of the epoch.

## Implementation

[LockedGold.sol](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/LockedGold.sol) manages the lifecycle of Locked `Map`.