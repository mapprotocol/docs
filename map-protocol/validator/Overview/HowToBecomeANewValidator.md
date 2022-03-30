## Prerequisites

### Hardware requirements

MAP is a Proof Of Stake network, which has different hardware requirements than a Proof of Work network.
Proof Of Stake consensus is less CPU intensive, but is more sensitive to network connectivity and latency.
Below is a list of standard requirements for running Validator on the MAP Network:

- Memory: 16 GB RAM
- CPU: Quad core 2.5 GHz (64-bit)
- Disk: 256 GB of SSD storage, plus a secondary HDD desirable
- Network: At least 100 Mb input/output Ethernet with a fiber Internet connection, ideally redundant connections and HA switches

### Software requirements

Building atlas requires git, Go (version 1.14 or later) and a C compiler. You can install them using your favourite package manager.

## How to become a new validator

In order to make your assets safer, we need you to set some necessary identification parameters to become a validator. We also set the corresponding threshold so that we can screen those who really want to contribute to the chain. Of course, we will give corresponding rewards to these people.

The following steps are regarded as your first operation, because you only need to perform the following operations once to become a validator. Unless you log off the validator or cancel the corresponding operation, you will not need to perform the second operation to avoid wasting your gas fee.

### Step 1: create Account

In this step, you need to transfer your identification information to the corresponding management contract,which will manage your account, keys, and metadata.

The purpose of this step is keep your locked `MAP` more secure by authorizing alternative keys to be used for signing attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with access to your locked `MAP` in storage.

You need `createAccount` command to perform the above operations,more detail about `createAccount` command please to see [this](../../marker/AboutCommon.md#CreateAccount).

### Step 2: locked `MAP`

The threshold we set to be the validator is to lock 1000,000 `MAP` into the corresponding management smart contract.

This part of the locked `MAP` will be used for future punishment, which is also one of the conditions for being elected.

You need `createAccount`  command to perform the above operations,more detail about `lockedMAP` command please to see [this](../../marker/AboutCommon.md#LockedMAP) .

### Step 3: validator register

This step is a key step for registering as a new validator.

You need `register`  command to perform the above operations,more detail about `register` command please to see [this](../../marker/AboutValidator.md#Register).

At this step, you will successfully register as a validator. Next, you can try to vote for yourself. How to vote please to see[this](../../marker/AboutVote.md#Vote).



## Example
If you want to become a validator, you need to meet two conditions:

1. A node that provides RPC services.
2. Your account needs to have at least 1,000,000 MAP.

### Start A node that provides RPC services.

You can start a node that provides RPC services by yourself, or you can use the [RPC nodes](../../../Makalu-PoC/PoC-2.md#atlas-address) we provide.

- Clone repositories
```shell
cd /Users/alex

git clone https://github.com/mapprotocol/atlas.git
```

- Build
```shell
cd /Users/alex/atlas

make atlas
```

- Start node
```shell
./atlas --datadir ./node-rpc --ipcpath ./node-rpc --port 36210 --http --http.addr "0.0.0.0" --http.port 7445 --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" console
```


### Create account
```shell
./marker createAccount --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T10-07-49.186973000Z--49d3112d06156761be64d91dd47ffe567fad60c8 --password "" --namePrefix "validator"

# Output
INFO [03-17|18:21:52.555] Create account                           func=createAccount address=0x49d3112D06156761bE64D91dD47fFE567fAD60c8 name=validator
INFO [03-17|18:21:52.557] === create Account === 
INFO [03-17|18:21:52.573] TxInfo                                   func=sendContractTransaction TX data nonce =0  gasLimit =4,500,000  gasPrice =1,001,000,000,000  chainID =212
INFO [03-17|18:21:52.574] Please waiting                           func=getResult                txHash =0x30cbc4eba0d4a607dbc7f7fd8c3d6a82c78aad756102cf0d56c7fa1b0c0daf15
INFO [03-17|18:21:56.210] Transaction Success                      func=queryTx                 block Number=18
INFO [03-17|18:21:56.210] === setName name === 
INFO [03-17|18:21:56.218] TxInfo                                   func=sendContractTransaction TX data nonce =1  gasLimit =4,500,000  gasPrice =1,001,000,000,000  chainID =212
INFO [03-17|18:21:56.219] Please waiting                           func=getResult                txHash =0x41b15c4dc9d577067c918a73e680b71913d786b863aebd002e8221215d7b4721
INFO [03-17|18:22:01.063] Transaction Success                      func=queryTx                 block Number=19
INFO [03-17|18:22:01.063] === setAccountDataEncryptionKey === 
INFO [03-17|18:22:01.070] TxInfo                                   func=sendContractTransaction TX data nonce =2  gasLimit =4,500,000  gasPrice =1,001,000,000,000  chainID =212
INFO [03-17|18:22:01.071] Please waiting                           func=getResult                txHash =0x7effb9701ab4dd3e73781aee4d8d7d4aee12e9edaed324b7f888ad87c9673935
INFO [03-17|18:22:06.718] Transaction Success                      func=queryTx                 block Number=20
```

### Locked MAP
```shell
./marker lockedMAP --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T10-07-49.186973000Z--49d3112d06156761be64d91dd47ffe567fad60c8 --password "" --lockedNum 1000000

# Output
INFO [03-17|18:22:37.829] === Lock  gold === 
INFO [03-17|18:22:37.830] Lock  gold                               amount=1000000000000000000000000
INFO [03-17|18:22:37.841] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =1,001,000,000,000  chainID =212
INFO [03-17|18:22:37.845] Please waiting                           func=getResult                txHash =0xe856030915f9dd50bc153270c1bf085e30e6b785739b746fe39f545392bcbdfc
INFO [03-17|18:22:41.073] Transaction Success                      func=queryTx                 block Number=27
```

### Validator register
```shell
./marker register --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T10-07-49.186973000Z--49d3112d06156761be64d91dd47ffe567fad60c8 --password "" --commission 0.6

# Output
INFO [03-17|18:22:59.751] === Register validator === 
INFO [03-17|18:22:59.751] === commision ===                        commision=600,000,000,000,000,000,000,000
INFO [03-17|18:22:59.854] TxInfo                                   func=sendContractTransaction TX data nonce =4  gasLimit =4,500,000  gasPrice =1,001,000,000,000  chainID =212
INFO [03-17|18:22:59.856] Please waiting                           func=getResult                txHash =0x2f555e30a59bf9891c38d08fce2b4cd03d107b362e1247c936358e50a5fcf815
INFO [03-17|18:23:01.074] Transaction Success                      func=queryTx                 block Number=31
```

### Verify
So far we have completed the registration steps of validator, now let's verify whether it has become a validator.

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T10-07-49.186973000Z--49d3112d06156761be64d91dd47ffe567fad60c8 --password ""

# Output
INFO [03-17|18:23:08.817] === getTotalVotesForEligibleValidators === admin=0x49d3112D06156761bE64D91dD47fFE567fAD60c8
INFO [03-17|18:23:08.820] Validator:                               addr=0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:08.820] Validator:                               addr=0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:08.820] Validator:                               addr=0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:08.820] Validator:                               addr=0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:08.820] Validator:                               addr=0x49d3112D06156761bE64D91dD47fFE567fAD60c8 vote amount=0
```
As can be seen from the above results, we have become a validator. But our vote count is 0, which prevents us from being 
elected as a validator that can participate in the block, which is not what we want. So we also need to vote for validators.

### Vote
We can use our validator account to vote for ourselves, or we can let other validators or voters vote for ourselves.
Below we will demonstrate voting for ourselves using our own validator account, as this is the easiest.

For more information on voting and elections, click on the links below to view:

[vote](./HowToVote.md)  
[election](../Election.md)

```shell
./marker vote --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/t/go_project/atlas/node-1/keystore/UTC--2022-03-17T10-07-49.186973000Z--49d3112d06156761be64d91dd47ffe567fad60c8 --password "" --validator "0x49d3112d06156761be64d91dd47ffe567fad60c8" --voteNum 100000

# Output
INFO [03-17|18:23:25.291] === vote Validator ===                   admin=0x49d3112D06156761bE64D91dD47fFE567fAD60c8 voteTargetValidator=0x49d3112D06156761bE64D91dD47fFE567fAD60c8 vote MAP Num=100000
INFO [03-17|18:23:25.312] TxInfo                                   func=sendContractTransaction TX data nonce =5  gasLimit =4,500,000  gasPrice =1,001,000,000,000  chainID =212
INFO [03-17|18:23:25.315] Please waiting                           func=getResult                txHash =0x00bd8e58b2fa8eea4ca0baeef020175b491647149fd429673cfa3c783a6420e4
INFO [03-17|18:23:27.132] Transaction Success                      func=queryTx                 block Number=34
```

### Verify
Let's verify that our vote was successful.

```shell
./marker getTotalVotesForEligibleValidators --rpcaddr 127.0.0.1 --rpcport 7445 --keystore /Users/alex/atlas/node-5/keystore/UTC--2022-03-17T10-07-49.186973000Z--49d3112d06156761be64d91dd47ffe567fad60c8 --password ""

# Output
INFO [03-17|18:23:37.547] === getTotalVotesForEligibleValidators === admin=0x49d3112D06156761bE64D91dD47fFE567fAD60c8
INFO [03-17|18:23:37.550] Validator:                               addr=0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C vote amount=11,000,000,000,000,000,000,000,000
INFO [03-17|18:23:37.550] Validator:                               addr=0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:37.550] Validator:                               addr=0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:37.550] Validator:                               addr=0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 vote amount=1,000,000,000,000,000,000,000,000
INFO [03-17|18:23:37.550] Validator:                               addr=0x49d3112D06156761bE64D91dD47fFE567fAD60c8 vote amount=100,000,000,000,000,000,000,000
```
Judging from the results, I've successfully voted for myself, but it's not enough.
We need to call RPC in the next epoch to finally determine whether we are selected as validators who can participate in 
block generation，Just like the following:

```shell
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":[],"id":1}' http://127.0.0.1:7445

# Output
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    "0xf930b74d2b1b703b879ab54e225ecc18ab28e61c",
    "0x0e2699be2b47dfd7560c4771a32a50b64f81293d",
    "0x3c0ef282c8c62a44ea2fe8928b6bd89a16fd8252",
    "0x41e4a55ef06c1961b3e143357e24ca7f92e4dd03",
    "0x49d3112d06156761be64d91dd47ffe567fad60c8" # our validator
  ]
}
```
For more information on istanbul_getValidators [click here](../../consensus/ConsensusAPI.md#getvalidators)