# Integrate an Exchange with MAPO Relay Chain

## Overview

The objective of this document is to provide a brief overview of how to integrate with the EVM-Compatible MAPO Relay Chain(Atlas). For teams that already support ETH, supporting the Atlas is as straightforward as spinning up an Atlas node (which has the same API as go-ethereum) and populating Atlas ChainID (22776) when constructing transactions.

## Integration using EVM Endpoints

### Running an Atlas node

you can get it from [source code](https://github.com/mapprotocol/atlas) or [release versin](https://github.com/mapprotocol/atlas/releases).

from source code:

```
// make sure the golang env

git clone https://github.com/mapprotocol/atlas.git
cd atlas & make atlas

```
than start a node with the RPC service on the background,use `atlas -h` get more details.

```
./build/bin/atlas --datadir ./data --gcmode "archive" --syncmode "full" --port 28360 --v5disc --http --http.addr "0.0.0.0" --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" --http.vhosts "*" 
```

## Interacting with the Atlas

Interacting with the Atlas is identical to interacting with [go-ethereum](https://geth.ethereum.org/). You can find the reference material for Atlas API [here](/sdk/RPC-API.md).

Please note that personal_ namespace is turned off by default. To turn it on, you need to pass the appropriate command line .


### Java SDK and Web3.js

you can use the [Java SDK](https://github.com/web3j/web3j) and [web3.js](https://web3js.readthedocs.io/en/v1.2.9/) libs interacting with the atlas.

If you plan on extracting data from the Atlas into your own systems using golang, we recommend using our custom [ethclient](https://github.com/mapprotocol/compass/tree/main/pkg/ethclient).

### Constructing transactions

Atlas transactions are identical to standard EVM transactions with one exceptions::

    They must be signed with Atlas’s ChainID (22776).


For development purposes, Atlas supports all the popular tooling for Ethereum,like as `MetaMask and Remix`,`Truffle` and `Hardhat`, so developers familiar with Ethereum and Solidity can feel right at home.

We are compatible with the improvement of Ethereum eip1559, and set the minimum basefee to 100GWei.


ps: Atlas consensus provides fast and irreversible finality with 5 seconds. To query the most up-to-date finalized block, query any value (i.e. block, balance, state, etc) with the latest parameter.