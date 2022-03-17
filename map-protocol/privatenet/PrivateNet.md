#  Operating a private network
Until now, atlas has both POC and POS version. Next, the following is a description of how to build private-net with four nodes On two different version. 

## Prerequisites

- Private key and address
  
  Building atlas requires git, Go (version 1.14 or later) and a C compiler. You can install them using your favourite package manager.

- Clone repositories
```
git clone https://github.com/mapprotocol/atlas.git
cd atlas
```

## Build four nodes

We can start four nodes with different ports on one host, or we can start four nodes on multiple hosts.
The following will use the node method of starting four different ports on one host to demonstrate.

- build atlas of POC version:
```
git checkout v0.2
make atlas
```

- build atlas of POS version:
```
make atlas
```

## Create four validator accounts 

- private key and address

  Atlas allows developers use the ethereum's account on atlas blockchain. If no account, it's helpful to use the command.
```shell
$ ./atlas --datadir ./node-1 account new
$ ./atlas --datadir ./node-2 account new
$ ./atlas --datadir ./node-3 account new
$ ./atlas --datadir ./node-4 account new

# Output
INFO [03-16|14:12:42.713] Maximum peer count                       ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Password: 
Repeat password: 

Your new key was generated

Public address of the key:   0x535f9A9d4C598289a892Cc5921E91862E740c72e
Path of the secret key file: node-1/keystore/UTC--2022-03-16T06-13-28.057575000Z--535f9a9d4c598289a892cc5921e91862e740c72e

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!

```

## Generating a genesis.json 
**Note that there are differences between versions, which the POC version needn't set validator.
Only Suitable for the POS version. The validator must be set, because only them are allowed to mine.**

Get validator configuration json and generating a genesis.json
- [generating a genesis.json ](../validator/Overview/HowToMakeGenesis.md#generating-a-genesis.json)

We now generate the genesis.json file using the paths of the four accounts generated in the previous step.
Fill the keystore path and account password corresponding to the account into the values corresponding to 
`Account` and `Password` key in the json file respectively. The value corresponding to the `AdminAddress` key 
can be any of the four accounts we created above or other accounts.

Just like the following:

```json
{
  "AdminAddress": "0x931abadeb878d761cf2df15f0ba0442350c132fc",
  "Validators": [
    {
      "Account": "/Users/alex/atlas/node-1/keystore/UTC--2022-03-16T06-13-28.057575000Z--535f9a9d4c598289a892cc5921e91862e740c72e",
      "Password": ""
    },
    {
      "Account": "/Users/alex/atlas/node-2/keystore/UTC--2022-03-16T06-38-33.638221000Z--2d7a789d78791ff62b220f6cbd5b50c6168ef0b5",
      "Password": ""
    },
    {
      "Account": "/Users/alex/atlas/node-3/keystore/UTC--2022-03-16T06-38-37.594508000Z--9d64de56141a501b8ecf6fbbaecde74f11c34ddf",
      "Password": ""
    },
    {
      "Account": "/Users/alex/atlas/node-4/keystore/UTC--2022-03-16T06-38-42.679038000Z--2cd61076834a033e8613ce8176bced9c7b48e8ed",
      "Password": ""
    }
  ]
}
```

## Initialize four validator nodes
Initialize the node with the genesis file generated in the previous step

```shell
./atlas --datadir ./node-1 init ./genesis.json
./atlas --datadir ./node-2 init ./genesis.json
./atlas --datadir ./node-3 init ./genesis.json
./atlas --datadir ./node-4 init ./genesis.json

# Output
INFO [03-16|15:14:56.031] Maximum peer count                       ETH=50 LES=0 total=50
INFO [03-16|15:14:56.038] Set global gas cap                       cap=50,000,000
INFO [03-16|15:14:56.038] Allocated cache and file handles         database=/Users/alex/atlas/node-1/atlas/chaindata cache=16.00MiB handles=16
INFO [03-16|15:14:56.133] Writing custom genesis block 
INFO [03-16|15:14:56.141] Persisted trie from memory database      nodes=276 size=31.68KiB time="921.915µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=-984.00B
INFO [03-16|15:14:56.142] Successfully wrote genesis state         database=chaindata                             hash=e359b1..ad2642
INFO [03-16|15:14:56.142] Allocated cache and file handles         database=/Users/alex/atlas/node-1/atlas/lightchaindata cache=16.00MiB handles=16
INFO [03-16|15:14:56.223] Writing custom genesis block 
INFO [03-16|15:14:56.226] Persisted trie from memory database      nodes=276 size=31.68KiB time="587.341µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=-984.00B
INFO [03-16|15:14:56.227] Successfully wrote genesis state         database=lightchaindata                        hash=e359b1..ad2642
```

## Start four validator nodes

The following command starts four corresponding nodes using the four accounts created earlier。

```shell
./atlas --datadir ./node-1 --ipcpath ./node-1 --port 20201 --mine --miner.validator 0x535f9a9d4c598289a892cc5921e91862e740c72e --unlock 0x535f9a9d4c598289a892cc5921e91862e740c72e console
./atlas --datadir ./node-2 --ipcpath ./node-2 --port 20202 --mine --miner.validator 0x2d7a789d78791ff62b220f6cbd5b50c6168ef0b5 --unlock 0x2d7a789d78791ff62b220f6cbd5b50c6168ef0b5 console
./atlas --datadir ./node-3 --ipcpath ./node-3 --port 20203 --mine --miner.validator 0x9d64de56141a501b8ecf6fbbaecde74f11c34ddf --unlock 0x9d64de56141a501b8ecf6fbbaecde74f11c34ddf console
./atlas --datadir ./node-4 --ipcpath ./node-4 --port 20204 --mine --miner.validator 0x2cd61076834a033e8613ce8176bced9c7b48e8ed --unlock 0x2cd61076834a033e8613ce8176bced9c7b48e8ed console
```

Type the above command on the command line and press enter, you will see the following prompt：

```shell
......
INFO [03-16|20:04:50.659] Starting peer-to-peer node               instance=atlas/v0.3.4-stable/darwin-amd64/go1.16.14
INFO [03-16|20:04:50.770] New local node record                    seq=1,647,431,881,694 id=54a701d30bf05010 ip=127.0.0.1 udp=20201 tcp=20201
INFO [03-16|20:04:50.770] Started P2P networking                   self=enode://8f8bb96b01be9f76a93b2b54bcaff35b48ace58f0b0f3c85a139048072ff030bcad022a442e49181885f1f0338a5efd2f4807d21d2f88973e18426dee3d8e0b7@127.0.0.1:20201 maxdialed=16 maxinbound=34
INFO [03-16|20:04:50.771] IPC endpoint opened                      url=/Users/t/go_project/atlas/node-1/node-1
Unlocking account 0x931abadeb878d761cf2df15f0ba0442350c132fc | Attempt 1/3
Password: 
```

At this time, we need to enter the password corresponding to the account and press Enter, congratulations, 
you have successfully started a node. Then we start the remaining nodes in the same way.

If you want to start an RPC node you can use the following way.
```shell
./atlas --datadir ./node-rpc --ipcpath ./node-rpc --port 20205 --http --http.addr "0.0.0.0" --http.port 7445 --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" console
```

## Connect four nodes

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
admin.addPeer("enode://8f8bb96b01be9f76a93b2b54bcaff35b48ace58f0b0f3c85a139048072ff030bcad022a442e49181885f1f0338a5efd2f4807d21d2f88973e18426dee3d8e0b7@127.0.0.1:20201")
admin.addPeer("enode://a4642d6eb2f1c69ee861f4146636df028a7eb328e233f620cc6838db474e94327bdcdc810d2f9c2fa30694764e71b4c7b5828f6e8df7a3f71f3eb781bb017a4e@127.0.0.1:20202")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@127.0.0.1:20203")
admin.addPeer("enode://88c2fdd0189a33e3b8ee02a04a767c4792140c00c08de5d368b9aac578a0a36b5518aee5fcb695cd93c348237901a5c532f561170adc00903001e40ca3eff041@127.0.0.1:20204")
```

## Check node status

You'll start seeing some output. After a few minutes, you should see lines that look like this. This means your node has connected other nodes and started produce blocks.
```text
INFO [03-15|13:39:27.683] Looking for peers                        peercount=2 tried=5 static=4
INFO [03-15|13:39:37.582] Reset timer to resend RoundChange msg    address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 func=resetResendRoundChangeTi
mer cur_seq=23 cur_epoch=1 cur_round=0 des_round=5 state="Waiting for new round" address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 timeout=17.5s
INFO [03-15|13:39:39.311] Looking for peers                        peercount=3 tried=0 static=4
```

