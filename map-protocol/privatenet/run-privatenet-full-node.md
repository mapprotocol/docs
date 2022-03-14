# Run a Map Full Node

## Run a Full Node


Full nodes play a special purpose in the Map ecosystem, acting as a bridge between the mobile wallets \(running as light clients\) and the validator nodes.

When a light client sends transactions, they may include a gateway fee to be paid to the node that gossips transactions to the other full nodes and validators.

For this reason, despite the fact that Map uses a proof-of-stake protocol, users can earn cryptocurrency without first having to own any, simply by running a full node.


## Prerequisites

Building atlas requires git, Go (version 1.14 or later) and a C compiler. You can install them using your favourite package manager.

## Build node 

clone repositories
```
git clone https://github.com/mapprotocol/atlas.git
cd atlas
```

build atlas: 
```
make atlas
```

## Create an account and get its address

atlas allows developers use the ethereum's account on atlas blockchain. 
if no account, it's helpful to use the command.
```
./atlas account new
```

## Set Validator and Relayer

the validator must be set, because only them are allowed to mine. 
set validator,see: [set validator](../validator/Validator-Tool-Marker-Genesis.md)

if it needs to cross-chain, just set relayer.
set relayer,see: [set relayer](../relayer/QuickStart.md)

## Start the node

This command specifies the settings needed to run the node, and gets it started.

```bash
./atlas --datadir ./node --ipcpath ./node --verbosity 0 --port 20201 --cache.preimages --http --http.port 7888 console
```

- tip: The command line above includes the parameter `--http.addr 0.0.0.0` which makes the Map Blockchain software listen for incoming RPC requests on all network adaptors.

## Connect other node

start atlas`s console, input command to connect validator node.
example:
```
admin.addPeer("enode://91009f4bee2fad0a5659ccf6b512bb43b8d70f7dcf098864622b576cb156ca35d50b55b4fa92966246ec2c4054a31169ee601c1532496c2ed07ef21acd65a262@13.67.79.15:21221")
admin.addPeer("enode://a4642d6eb2f1c69ee861f4146636df028a7eb328e233f620cc6838db474e94327bdcdc810d2f9c2fa30694764e71b4c7b5828f6e8df7a3f71f3eb781bb017a4e@13.67.118.60:21221")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@13.76.138.119:21221")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@168.63.248.220:21221")
```


You'll start seeing some output. After a few minutes, you should see lines that look like this. This means your node has started syncing with the network and is receiving blocks.

```text
INFO [07-16|14:04:24.924] Imported new chain segment               blocks=139  txs=319 mgas=61.987 elapsed=8.085s mgasps=7.666 number=406  hash=9acf16…4fddc8 age=6h58m44s cache=1.51mB
INFO [07-16|14:04:32.928] Imported new chain segment               blocks=303  txs=179 mgas=21.837 elapsed=8.004s mgasps=2.728 number=709  hash=8de06a…77bb92 age=6h33m37s cache=1.77mB
INFO [07-16|14:04:40.918] Imported new chain segment               blocks=411  txs=0   mgas=0.000  elapsed=8.023s mgasps=0.000 number=1120 hash=3db22a…9fa95a age=5h59m30s cache=1.92mB
INFO [07-16|14:04:48.941] Imported new chain segment               blocks=335  txs=0   mgas=0.000  elapsed=8.023s mgasps=0.000 number=1455 hash=7eb3f8…32ebf0 age=5h31m43s cache=2.09mB
INFO [07-16|14:04:56.944] Imported new chain segment               blocks=472  txs=0   mgas=0.000  elapsed=8.003s mgasps=0.000 number=1927 hash=4f1010…1414c1 age=4h52m31s cache=2.34mB
```

You will have fully synced with the network once you have pulled the latest block number

