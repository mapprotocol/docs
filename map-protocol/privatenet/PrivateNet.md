#  Operating a private network
Until now, atlas has both POC and POS version. Next, the following is a description of how to build private-net On two different version.

## Prerequisites

Building atlas requires git, Go (version 1.14 or later) and a C compiler. You can install them using your favourite package manager.

## Build node 

- clone repositories,
```
git clone https://github.com/mapprotocol/atlas.git
cd atlas
```

- build atlas of POC version:
```
git checkout v0.2
make atlas
```

- build atlas of POS version:
```
make atlas
```

## Create an account

atlas allows developers use the ethereum's account on atlas blockchain. If no account, it's helpful to use the command.
```shell
$ ./atlas account new
```

- genesis bls public key. 
  
Refer to `TestMakeKey` function from https://github.com/mapprotocol/atlas/blob/main/tools/makeValidator_test.go


## Set Validator and Relayer
**Note that there are differences between versions, which the POC version needn't set validator.**

Only Suitable for the POS version. The validator must be set, because only them are allowed to mine. 
How to set validator,see: 
- [set validator on contract](../validator/Validator-Tool-Marker-Genesis.md)
- [set validator on genesis block](../relayer/QuickStart.md)


If it needs to cross-chain, just set relayer. In the first epoch,the relayer address is as same as validator.
How to set relayer,see:
- [set relayer on genesis block](../relayer/QuickStart.md)

## start the node

This command specifies the settings needed to run the node, and gets it started.

```shell
$ ./atlas --datadir ./node --ipcpath ./node --verbosity 0 --port 20201 --cache.preimages --http --http.port 7888 console
```

- tip: The command line above includes the parameter `--http.addr 0.0.0.0` which makes the Map Blockchain software listen for incoming RPC requests on all network adaptors.

In a private network setting, however a single CPU miner instance is more than enough for
practical purposes as it can produce a stable stream of blocks at the correct intervals
without needing heavy resources (consider running on a single thread, no need for multiple
ones either). To start a `atlas` instance for mining, run it with all your usual flags, extended
by:

```shell
$ atlas <usual-flags> --miner.etherbase=<validator-address>
```

Which will start mining blocks and transactions on a single CPU thread, crediting all
proceedings to the account specified by `--miner.etherbase`. You can further tune the mining by changing the default gas price transactions converge to (`--miner.gasprice`).


## Connect the node

-  the Operation of flag. 

With the bootnode operational and externally reachable (try
`telnet <ip> <port>` to ensure it's indeed reachable), start every subsequent `atlas`
node pointed to the bootnode for peer discovery via the `--bootnodes` flag. It will
probably also be desirable to keep the data directory of your private network separated, so
do also specify a custom `--datadir` flag.

```shell
$ atlas --datadir=path/to/custom/data/folder --bootnodes=<bootnode-url>
```

- the Operation on a console.

Query Own node-url: 
```
admin.nodeInfo.enode
"enode://6a469a852c7ee7f656e33a7858db5c480d6253ad760771a779985c305d66de78109bae579adf3f32fbd770e44145d5728176bcddda56d8a8c92709f827d94842@127.0.0.1
:20201"
```

Connect validator node, for example:
```
admin.addPeer("enode://91009f4bee2fad0a5659ccf6b512bb43b8d70f7dcf098864622b576cb156ca35d50b55b4fa92966246ec2c4054a31169ee601c1532496c2ed07ef21acd65a262@13.67.79.15:21221")
admin.addPeer("enode://a4642d6eb2f1c69ee861f4146636df028a7eb328e233f620cc6838db474e94327bdcdc810d2f9c2fa30694764e71b4c7b5828f6e8df7a3f71f3eb781bb017a4e@13.67.118.60:21221")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@13.76.138.119:21221")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@168.63.248.220:21221")
```

You'll start seeing some output. After a few minutes, you should see lines that look like this. This means your node has connected other nodes and started produce blocks.
```text
INFO [03-15|13:39:27.683] Looking for peers                        peercount=2 tried=5 static=4
INFO [03-15|13:39:37.582] Reset timer to resend RoundChange msg    address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 func=resetResendRoundChangeTi
mer cur_seq=23 cur_epoch=1 cur_round=0 des_round=5 state="Waiting for new round" address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 timeout=17.5s
INFO [03-15|13:39:39.311] Looking for peers                        peercount=3 tried=0 static=4
```

