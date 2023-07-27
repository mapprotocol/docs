## Proof-of-Stake

MAP Relay Chain(Atlas) is a proof-of-stake blockchain. In comparison to Proof of Work systems like Bitcoin and Ethereum, this eliminates the negative environmental impact and means that users can make transactions that are cheaper, faster, and whose outcome cannot be changed once complete.

MAP Relay Chain implements a Istanbul Byzantine Fault Tolerant (IBFT) consensus algorithm in which a well-defined set of validator nodes broadcast signed messages between themselves in a sequence of steps to reach agreement even when up to a third of the total nodes are offline, faulty or malicious. When a quorum of validators have reached agreement, that decision is final.

## Validators
Validators gather transactions received from other nodes and execute any associated smart contracts to form new blocks, then participate in a Byzantine Fault Tolerant (BFT) consensus protocol to advance the state of the network. Since BFT protocols can scale only to a few hundred participants, and can tolerate at most a third of the participants acting maliciously, a proof-of-stake mechanism admits only a limited set of nodes to this role.

## Staking Requirements

Atlas uses a proof-of-stake consensus mechanism, which requires Validators to have locked `MAPO` to participate in block production. 
The current requirement is `1,000,000` `MAPO` to register a Validator.

## About election

The Election contract is called from the IBFT block finalization code to select the validators for the following epoch.
The contract maintains a sorted list of the Locked `MAPO` voting (either pending or activated) for each Validator.
The active validator set is updated by running an election in the final block of each epoch, after processing transactions and Epoch Rewards.

There is a minimum target and a maximum cap on the number of active validators that may be selected.
If the minimum target is not reached, the election aborts and no change is made to the validator set this epoch.

## Validator number and Reward

The participators make these decisions by locking `MAPO` and voting for Validator.
Validator elections are held `every epoch` (approximately once per three day).
The protocol elects a maximum of `100` Validators. At each epoch, every elected Validator must be re-elected to continue.
Validators are selected in proportion to votes received for each Validator.

If you hold `MAPO`, or are a beneficiary of a Release`MAPO` contract that allows voting, you can vote for validator. A single account can split their Locked`MAPO` balance to have outstanding votes between `3 validator` and `10 validator`.
`MAPO` that you lock and use to vote for a  validator receives epoch rewards every epoch (approximately every day) once the community passes a governance proposal enabling rewards.

