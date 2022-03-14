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

## Start the node

This command specifies the settings needed to run the node, and gets it started.

```bash
./atlas --datadir ./node --ipcpath ./node --verbosity 0 --port 20201 --cache.preimages --http --http.port 7888 console
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

- tip: The command line above includes the parameter `--http.addr 0.0.0.0` which makes the Map Blockchain software listen for incoming RPC requests on all network adaptors.
