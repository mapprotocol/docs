# MAP Omnichain Service (MOS) Message

## What is MOS Message

MOS message enables projects built on one chain to easily synchronize some project information to other chains, and can also call contract methods on other connected chains.

MOS uses MAP Protocol light client to verify the transaction of cross-chain messages to ensure that cross-chain messages are authentic and checkable on the chain.

With MOS you can achieve interoperation with two chains:

- Call a contract on chain B from chain A.
- Pack the message changes in chain A and write them into chain B to realize message synchronization

## How it works

See the [details](/develop/mos/message/cross-chain-message.md) here.

## Build dApps on MOS message
Here give some examples that how to build dapps on MOS message.
* [OmniDictionary](/develop/mos/examples/OmniDictionary.md)
