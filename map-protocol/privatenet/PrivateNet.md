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

Public address of the key:   0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C
BLS Public address of the key:   0x28a23c223e4f46e2967a4332dc28bf883f58d8dd141cd006ba2c6e8c8fb14c1f00
BLSProofOfPossession:   0x0139b981ed2621470e10d1e3f41812663ca61cfa0c3e8e1aca84d0f4f64e80bc5c6745844f218ca9e68363dce54bc21cbf772d2409246b9d9886f324d76c7605777c92333e490607f8732ed8bcdf135fd4bb3ea5e770cf3abffe38fa84edf78188339b159392aca1d6af621e67fb741eaefd51e1d31c7a0aefb690c23f075c3bb9
PublicKeyHex:   0x04cb33a2402dc01be2470222e30ffcb236fcf1926837358a5fd7bf2b9704115ca3857d9e41009301cf6e021fa2700eca8393c84e9a9cdf4178b0c9fbc9cdce1ccc
Path of the secret key file: node-1/keystore/UTC--2022-03-17T09-35-15.650368000Z--f930b74d2b1b703b879ab54e225ecc18ab28e61c

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
  "AdminAddress": "0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C",
  "Validators": [
    {
      "Address": "0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C",
      "PublicKeyHex": "0x04cb33a2402dc01be2470222e30ffcb236fcf1926837358a5fd7bf2b9704115ca3857d9e41009301cf6e021fa2700eca8393c84e9a9cdf4178b0c9fbc9cdce1ccc",
      "BLSPubKey": "0x28a23c223e4f46e2967a4332dc28bf883f58d8dd141cd006ba2c6e8c8fb14c1f00",
      "BLSProofOfPossession": "0x0139b981ed2621470e10d1e3f41812663ca61cfa0c3e8e1aca84d0f4f64e80bc5c6745844f218ca9e68363dce54bc21cbf772d2409246b9d9886f324d76c7605777c92333e490607f8732ed8bcdf135fd4bb3ea5e770cf3abffe38fa84edf78188339b159392aca1d6af621e67fb741eaefd51e1d31c7a0aefb690c23f075c3bb9"
    },
    {
      "Address": "0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d",
      "PublicKeyHex": "0x04e42d6217bd5943b395706d959451028b2a2bde27091729ec8a9a85ea08c42a8917cc8bcddd39abf60af4c647d5e345e0e3b4e68912f5d7729267f3427cb7692c",
      "BLSPubKey": "0x25cc3db8313960693f32826d23399e4ab77b46782503bc42f1a6c2afd7e3041c00",
      "BLSProofOfPossession": "0x013d9147f769934037e8ddd00a686f21ece0c68dfbf666560dc6c0c0c4fab49edd0eabc5b59f8b41cf0f7f7fb17fb7541f5737cece4a18bbc05cf691d3c66532f0313629e6801097940ef01b8652eb1d53d78e7efe2d21bd26d40a4a612229a2ac72be9ea8dc1365414f51753b9532adf9921f026ee584799d1f64c328df65851c"
    },
    {
      "Address": "0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252",
      "PublicKeyHex": "0x04e9527daab62c13afa7068fe902b62cd6c1d067b1553f57f8d31e5887b640b594c8aa34cdc1e43630041eaa20063e27e6acf6379f82c5049818a079cc888eef49",
      "BLSPubKey": "0x1a5c9f6f2f0ce12cf54aca83d760a2d0305d221a2f8bfa723430030bc3e98f6500",
      "BLSProofOfPossession": "0x010a8d302da72577c064b079a4855a85ed8903f18dce78ab4e779fa8f99c9ee26b55894a5bdb471fcbb12a7317f2f64e9ffddbe2639ae9485a9fa49e358eb8a35b7ec0b1e02eabc2e7053d0337b4eaa0d5266f5411c56c358b87b479a62488b2112d4ac4de47910d7f6b40ac806cba554d9abb82c03e27a54ca2536c62bd779ee0"
    },
    {
      "Address": "0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03",
      "PublicKeyHex": "0x048e7157847573e11a80593c01b86cd59f40746e3389dcf96ec7dfac39fb20bb8df8e426a9573b556acead607f17512f4a0799d86e7ced7bc2c31e66bd2c40e3e0",
      "BLSPubKey": "0x1a5c9f6f2f0ce12cf54aca83d760a2d0305d221a2f8bfa723430030bc3e98f6500",
      "BLSProofOfPossession": "0x010a8d302da72577c064b079a4855a85ed8903f18dce78ab4e779fa8f99c9ee26b55894a5bdb471fcbb12a7317f2f64e9ffddbe2639ae9485a9fa49e358eb8a35b7ec0b1e02eabc2e7053d0337b4eaa0d5266f5411c56c358b87b479a62488b2112d4ac4de47910d7f6b40ac806cba554d9abb82c03e27a54ca2536c62bd779ee0"
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
./atlas --datadir ./node-1 --ipcpath ./node-1 --port 20201 --mine --miner.validator 0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C --unlock 0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C console
./atlas --datadir ./node-2 --ipcpath ./node-2 --port 20202 --mine --miner.validator 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d --unlock 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d console
./atlas --datadir ./node-3 --ipcpath ./node-3 --port 20203 --mine --miner.validator 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 --unlock 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 console
./atlas --datadir ./node-4 --ipcpath ./node-4 --port 20204 --mine --miner.validator 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 --unlock 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 console
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

