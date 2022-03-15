# Elections

Introduction to Makalu chain validator elections and management of validator and votes throughout the process.

## Updating the Active Validator Set

The active validator set is updated by running an election in the final block of each epoch, after processing transactions and Epoch Rewards.

## Election Validator 

Validators must have at least 0.01 proportion of the total votes to be considered for the election. So the validator can't have no voters.

The advantage of this is to avoid burn gold and limit the number of voters in the 1000.

There is a minimum target and a maximum cap on the number of active validators that may be selected. If the minimum target is not reached, the election aborts and no change is made to the validator set this epoch.