# Voting

Introduction to commands related to voting operations

## vote

Vote for the specified validator.

You must [locked](./common.md#lockedmap) enough MAPO tokens in the LockedGold contract
in advance and register your information in the contract.

This operation will decrease the total votes and unvoted amount of your previously registered account in the LockedGold
contract, and increase the total votes and pending votes of the validator in the Election contract.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC
  service address.
- `keystore`: The path to the keystore file.
- `target`: The address of the validator you want to vote for.
- `voteNum`: The number of MAPO tokens you want to vote with.

```shell
./marker vote
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000
```

## quicklyVote

If you have not created an account and locked MAPO tokens yet, you can quickly vote using the quicklyVote command, which
integrates the createAccount, lockedMAP, and vote commands. Please note that you can only use this command once.
Regardless of the success or failure of the command, it includes the operations corresponding to the createAccount,
lockedMAP, and vote commands and does not have the feature of being reusable.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.
- `target`: The address of the validator you want to vote for.
- `voteNum`: The number of MAPO tokens you want to vote with.
- `lockedNum`: The number of MAPO tokens you want to lock.

```shell
./marker quicklyVote
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--voteNum 10000
--lockedNum 10000
```

## activate

Activate pending votes to start earning rewards.

As a voter, you need to activate your pending votes at some point after the end of the epoch in which the pending votes
were generated. This means converting the pending votes of your account into active votes.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.
- `target`: The address of the validator for which you want to activate votes.

```shell
./marker activate
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
```

## revokePending

Revoke pending votes for a validator.

This command converts the voting state of MAPO tokens to non-voting MAPO tokens and increases the total votes and
non-votes of your previously registered account in the LockedGold contract. In the Election contract, it decreases the
total votes and pending votes of the validator.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.
- `target`: The address of the validator for which you want to revoke pending votes.
- `lockNum`: The number of MAPO tokens you want to revoke from the validator's pending votes.

```shell
./marker revokePending
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--lockedNum 10000
```

## revokeActive

Revoke active votes for a validator.

This command converts voting MAPO tokens to non-voting MAPO tokens. It increases the total votes and non-votes of your
previously registered account in the LockedGold contract. In the Election contract, it decreases the total votes and
active votes of the validator.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.
- `target`: The address of the validator for which you want to revoke active votes.
- `lockNum`: The number of MAPO tokens you want to revoke from the validator's active votes.

```shell
./marker revokeActive
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--target "0x81f02fd21657df80783755874a92c996749777bf"
--lockedNum 10000
```
