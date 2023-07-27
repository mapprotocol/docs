# Elections

Introduction to Map chain validator elections and management of validator and votes throughout the process.

## Updating the Active Validator Set

The active validator set is updated by running an election in the final block of each epoch, after processing transactions and Epoch Rewards.

## Election Validator 

Validators must have at least 0.001 proportion of the total votes to be considered for the election. So the validator can't have no votes.

The advantage of this is to avoid burn `MAP` and limit the number of voters in the 1000.

There is a minimum target(1) and a maximum cap(100) on the number of active validators that may be selected. If the minimum target is not reached, the election aborts and no change is made to the validator set this epoch.

EXAMPLE:
Now there are four validators on the chain,they are:

["0x1c0eDab88dbb72B119039c4d14b1663525b3aC15", "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF", "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11", "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5"]

If we do not select validators for some reasons (equivalent validators number to less than 1), we will continue to use the above validators.
If we select the latest set of validators (which means that the number of new validators is greater than 1 and less than 100), we will replace the above validators with new validators.

## Implementation

[Election.sol](https://github.com/mapprotocol/atlas-contracts/blob/main/contracts/governance/Election.sol) manages Locked `Map` voting and epoch rewards and runs Validator Elections.


