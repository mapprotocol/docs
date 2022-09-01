## How to become a new validator advanced

In order to make your assets safer, we need you to set some necessary identification parameters to become a validator.
We also set the corresponding threshold so that we can screen those who really want to contribute to the chain. Of
course, we will give corresponding rewards to these people.

The following steps are regarded as your first operation, because you only need to perform the following operations once
to become a validator. Unless you log off the validator or cancel the corresponding operation, you will not need to
perform the second operation to avoid wasting your gas fee.

### Step 1: create account

In this step, you need to transfer your identification information to the corresponding management contract, which will
manage your account, keys, and metadata.

The purpose of this step is keep your locked `MAP` more secure by authorizing alternative keys to be used for signing
attestations、voting、validating. By doing so, you can continue to participate in the protocol while keeping the key with
access to your locked `MAP` in storage.

You need `createAccount` command to perform the above operations,more detail about `createAccount` command please to
see [this](/develop/map-relay-chain/marker/AboutCommon.md#createaccount).

### Step 2: authorize

Authorizes an address to sign consensus messages on behalf of the account. This authorized address is called the signer.
As his name is, he is only responsible for signing, your reward will not be issued to the signer, but to the account
created in the previous step。

### Step 3: locked `MAP`

The threshold we set to be the validator is to lock 1000,000 `MAP` into the corresponding management smart contract.

This part of the locked `MAP` will be used for future punishment, which is also one of the conditions for being elected.

You need `lockedMAP`  command to perform the above operations,more detail about `lockedMAP` command please to
see [this](/develop/map-relay-chain/marker/AboutCommon.md#lockedmap) .

### Step 4: validator register

This step is a key step for registering as a new validator.

You need `register`  command to perform the above operations,more detail about `register` command please to
see [this](/develop/map-relay-chain/marker/AboutValidator.md#register).

At this step, you will successfully register as a validator. Next, you can try to vote for yourself. How to vote please
to see[this](/develop/map-relay-chain/marker/AboutVote.md#vote).

### Step 5: vote

Validators must have at least 0.001 proportion of the total votes to be considered for the election. So the validator
can't have no votes.

We can use our validator account to vote for ourselves, or we can let other validators or voters vote for ourselves.

We've locked in 1,000,000 MAP in step 3, and it's a great decision to vote for ourselves now

## Advanced example

### Start A node that provides RPC services.

You can start a node that provides RPC services by yourself, or you can use
the [RPC nodes](/develop/map-relay-chain/Makalu-PoC/PoC-2.md#atlas-address) we provide.

- Clone repositories

```shell
cd /Users/alex

git clone https://github.com/mapprotocol/atlas.git && cd atlas
```

- Build

```shell
make atlas
make marker
```

The file generated by build is in the build/bin directory

### Join the network

First, you need to prepare two keystore, one for staking is called account, and one for mining is called signer

keystore of `account`: account.json

keystore of `signer`:  signer.json

How to build the atlas [click here](/develop/map-relay-chain/make-private-chain.md#build-four-nodes)

If you want node to run in the background and not hang up, you can use nohup and & in combination, or screen or similar.
Below we will demonstrate using screen

--miner.validator is used to specify the address of the `signer`

```shell
./atlas --datadir ./node --syncmode "full" --port 30321 --mine --miner.validator 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 --unlock 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8

INFO [08-01|16:15:45.369] Bumping default cache on mainnet         provided=1024 updated=4096
INFO [08-01|16:15:45.370] Maximum peer count                       ETH=50 LES=0 total=50
INFO [08-01|16:15:45.371] Set global gas cap                       cap=50,000,000
INFO [08-01|16:15:45.371] Allocated trie memory caches             clean=614.00MiB dirty=1024.00MiB
INFO [08-01|16:15:45.371] Allocated cache and file handles         database=/Users/t/data/atlas-1/atlas/chaindata cache=2.00GiB handles=5120
INFO [08-01|16:15:45.722] Opened ancient database                  database=/Users/t/data/atlas-1/atlas/chaindata/ancient readonly=false
......

> net
{
  listening: true,
  peerCount: 6,
  version: "22776",
  getListening: function(callback),
  getPeerCount: function(callback),
  getVersion: function(callback)
}

......

INFO [08-01|16:40:40.796] Finalized                                func=Finalize        block=1 epochSize=50000 duration="333.422µs" lastInEpoch=false
INFO [08-01|16:40:40.797] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=5.391ms mgasps=0.000 number=1 hash=62d029..14f4c1 age=1mo4d23h dirty=7.07KiB
INFO [08-01|16:40:51.790] Finalized                                func=Finalize        block=2 epochSize=50000 duration="106.969µs" lastInEpoch=false
INFO [08-01|16:40:51.790] Imported new chain segment               blocks=1 txs=0 mgas=0.000 elapsed=4.913ms mgasps=0.000 number=2 hash=c2b741..35869d age=1mo4d23h dirty=10.79KiB
INFO [08-01|16:40:53.892] Finalized                                func=Finalize        block=3 epochSize=50000 duration="104.285µs" lastInEpoch=false
INFO [08-01|16:40:53.893] Finalized                                func=Finalize        block=4 epochSize=50000 duration="105.568µs" lastInEpoch=false
```

After the node starts, it will automatically connect to other nodes, and then start to synchronize blocks.

### Singer operations

### Make ECDSA signature from signer

--validator is used to specify the address of the `account`

--signerPriv is used to specify the private key of the `signer`

```shell
./marker makeECDSASignatureFromSigner --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25

INFO [08-26|17:31:49.422] === makeECDSASignatureFromSigner === 
INFO [08-26|17:31:49.422] === signer  ===                          account=0x26654Eb0Bb935DCE4a34DAA3e14c67662a8Aa1f8
INFO [08-26|17:31:49.422] ECDSASignature                           result=0x59dff185...32f0d700

```

### Generate signer proof

--validator is used to specify the address of the `account`

--signerPriv is used to specify the private key of the `signer`

```shell
./marker generateSignerProof --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --signerPriv 040939e5...604b6f25

INFO [08-26|17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26|17:32:23.955] === makeBLSProofOfPossessionFromSigner === 
INFO [08-26|17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1

```

### Account operations

### Create account

--keystore is used to specify the address of the `account`

```shell
./marker createAccount -rpcaddr http://127.0.0.1:7445 --keystore ./account.json --password "" --name "validator"

INFO [07-08|14:54:28.097] Create account                           func=createAccount address=0x73bC690093b9dD0400c91886184A60cC127b2c33 name=validator
INFO [07-08|14:54:28.097] === create Account === 
INFO [07-08|14:54:28.102] TxInfo                                   func=sendContractTransaction TX data nonce =0  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:28.103] Please waiting                           func=getResult                txHash =0xa8d9dfb5b74720a940e904972784225d605ebb111fe92b87184ccb3a54ffd452
INFO [07-08|14:54:30.726] Transaction Success                      func=queryTx                 block Number=9
INFO [07-08|14:54:30.726] === setName name === 
INFO [07-08|14:54:30.731] TxInfo                                   func=sendContractTransaction TX data nonce =1  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:30.732] Please waiting                           func=getResult                txHash =0xf26f4f8b4a7f6ab44a70fe474162fe22b1bcd7694b4c1a42ed0b1a70d062d5ee
INFO [07-08|14:54:35.779] Transaction Success                      func=queryTx                 block Number=10
INFO [07-08|14:54:35.779] === setAccountDataEncryptionKey === 
INFO [07-08|14:54:35.784] TxInfo                                   func=sendContractTransaction TX data nonce =2  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:35.785] Please waiting                           func=getResult                txHash =0x430533536a6305b603c9abd6282a4fec359c7f42b8dbd78cf4d3f340ec9bf31b
INFO [07-08|14:54:40.224] Transaction Success                      func=queryTx                 block Number=11
```

### Authorize by signature

Authorize using the signature (0x59dff185...32f0d700) generated
from [Make ECDSA signature from signer](/run/HowToBecomeANewValidatorAdvanced.md#make-ecdsa-signature-from-signer)

--keystore is used to specify the keystore of the `account` (account.json)

--signer is used to specify the address key of the `signer` (signer.json)

```shell
./marker authorizeValidatorSignerBySignature -rpcaddr http://127.0.0.1:7445 --keystore ./account.json --password "" --signer 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 --signature 0x59dff185...32f0d700

INFO [07-08|14:55:00.015] authorizeValidatorSignerBySignature      signer=0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8    signature=0x59dff185...32f0d700
INFO [07-08|14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08|14:55:05.078] Transaction Success                      func=queryTx                  block Number=16
```

### Locked MAP

--keystore is used to specify the keystore of the `account`

```shell
./marker lockedMAP -rpcaddr http://127.0.0.1:7445 --keystore ./account.json --password "" --lockedNum 1000000

INFO [07-08|14:54:49.141] === Lock  gold === 
INFO [07-08|14:54:49.141] Lock  gold                               amount=1000000000000000000000000
INFO [07-08|14:54:49.148] TxInfo                                   func=sendContractTransaction TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|14:54:49.150] Please waiting                           func=getResult                txHash =0x698140b0ad8677706a4d10d3c5c72f15a8e143623be84a6ed514990fe3f5e5f3
INFO [07-08|14:54:50.765] Transaction Success                      func=queryTx                 block Number=13
```

### Validator register by proof

Register the validator using the proof (0xf90149b8...0e56f0ab1) generated
by [Generate signer proof](/run/HowToBecomeANewValidatorAdvanced.md#generate-signer-proof)

--keystore is used to specify the keystore of the `account`

```shell
./marker registerByProof -rpcaddr http://127.0.0.1:7445 --keystore ./account.json --password "" -- --proof 0xf90149b8...0e56f0ab1 

INFO [08-26|17:32:25.055] registerValidatorByProof                 commission=1,000,000
INFO [08-26|17:32:25.548] === getTotalVotesForValidator ===        admin=0x73bc690093b9dd0400c91886184a60cc127b2c33
INFO [08-26|17:32:25.974] === getTotalVotesForValidator ===        result=0
INFO [08-26|17:32:26.011] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [08-26|17:32:26.119] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [08-26|17:32:27.241] Transaction Success                      func=queryTx                 block Number=194
```

### Verify

So far we have completed the registration steps of validator, now let's verify whether it has become a validator.

```shell
./marker getTotalVotesForEligibleValidators -rpcaddr http://127.0.0.1:7445

INFO [07-08|15:10:03.301] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:10:03.301] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=0

```

As can be seen from the above results, we have become a validator. But our vote count is 0, which prevents us from being
elected as a validator that can participate in the block, which is not what we want. So we also need to vote for
validators.

### Vote

The number of votes cannot be greater than the number of votes locked.

For more information on voting and elections, click on the links below to view:

[vote](/develop/map-relay-chain/how-to-vote.md)  
[election](/develop/map-relay-chain/consensus/Election.md)

--keystore is used to specify the keystore of the `account`

```shell
./marker vote -rpcaddr http://127.0.0.1:7445 --keystore ./account.json --password "" --validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 --voteNum 1000000

INFO [07-08|15:11:13.693] === vote Validator ===                   admin=0x73bc690093b9dd0400c91886184a60cc127b2c33 voteTargetValidator=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote MAP Num=1000000
INFO [07-08|15:11:13.709] TxInfo                                   func=sendContractTransaction TX data nonce =4  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [07-08|15:11:13.710] Please waiting                           func=getResult                txHash =0x8ec67797871ea313f7beea33900db8f680ddf2d01f35894c65e1212151729747
INFO [07-08|15:11:15.123] Transaction Success                      func=queryTx                 block Number=210
```

### Verify

Let's verify that our vote was successful.

```shell
./marker getTotalVotesForEligibleValidators -rpcaddr http://127.0.0.1:7445

INFO [07-08|15:21:45.881] Validator:                               addr=0xeA9efaA232A4567EaC21C8C096f8BfF84595A244 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x6ACdC02223100189d82A958d888F54fA27d60e8A vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0xA53516D49A72019692Ac69cB42641942597654f6 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x5d643Dfb9ae372ce4Fdbc80890156E2CD8290846 vote amount=1,000,000,000,000,000,000,000,000
INFO [07-08|15:21:45.881] Validator:                               addr=0x73bC690093b9dD0400c91886184A60cC127b2c33 vote amount=1,000,000,000,000,000,000,000,000

```

Judging from the results, I've successfully voted for myself, but it's not enough. We need to call RPC in the next epoch
to finally determine whether we are selected as validators who can participate in block generation，Just like the
following:

```shell
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":[],"id":1}' http://127.0.0.1:7445

# Output
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "0xb4e1bc0856f70a55764fd6b3f8dd27f2162108e9",
        "0x7a3a26123dbd9cfefc1725fe7779580b987251cb",
        "0x7607c9cdd733d8cda0a644839ec2bac5fa180ed4",
        "0x65b3fee569bf82ff148bdded9c3793fb685f9333",
        "0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8" # our signer
    ]
}
```

For more information on istanbul_getValidators [click here](/sdk/ConsensusAPI.md#getvalidators)