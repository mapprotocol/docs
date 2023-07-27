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
git checkout v1.0.0 or git checkout <latest tag>
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

Address:   0x57d28a6Cb48dc6Da45f6fE13E42757aF56cB0650
PublicKey:   0x04cb8512a050dc66750faabf4a6cace5bbfd70ccaa5e94e986f5062aa5c016a7e9541f266b7289e6e113c9780fbf5d2ad1ff3136576a38e20569f82094c2e47d30
BLS Public key:   0x2a9520c996aaa5bd3222a0cef50ae1420602b4ba836c3e0a590d794107d8d7b51139baefa4645947402dc31d3c69ae96a83f054b0851412892d03ba6d154257126ebfcd6c512f84f19d139749ce760b9f142bd10c9a80b0f6b3086254840a4ee0d9510c2ebfcbbf03d168d62d8b2926edeee70e0e8b6b205e0ac0394370832ec
BLS G1 Public key:   0x0491aa3f6523e253003c02856a4623a163bb11ba9f115ae464c940dd60e64e0b1fff5644ca887928f0c6a29055bfa608103aa19d13edfc1a12f5baaa69d60f7a
BLSProofOfPossession:   0x2866621aa6703c6992299f75ee05b34fb42963ae82173e43c212d891ddc8a94e107fa37f3833e9230deb3078dde2521ea6b833e70ed051ee297734117bec367c

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
- [generating a genesis.json ](./how-to-make-genesis.md#generating-a-genesis.json-file)

We now generate the genesis.json file using the paths of the four accounts generated in the previous step.
Fill in the information we output when we created the account into the corresponding key value.
The value corresponding to the `AdminAddress` key can be any of the four accounts we created above or other accounts.

Just like the following:

```json
{
  "AdminAddress": "0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12",
  "Validators": [
    {
      "Address": "0xB16561A66B6439944DAf0388b5E6a2D3D0a49e12",
      "SignerAddress": "0x1c0eDab88dbb72B119039c4d14b1663525b3aC15",
      "ECDSASignature": "0x2ac44a912dbf3205f0500401cf02db33240fa03198dd4c3b202dfe7be3dbe1ed7cbfbe9326d50223faf2971e3ecefed027df1dda6727e9c45b55b377384dacf201",
      "PublicKeyHex": "0x0491c373a1504d67e0c6c98276fc6043544fd09a623473b6936a107943baf666612b5e2a3beacf839d1ec74fd00f4388d4b813eac26b26ab4859003473b286650a",
      "BLSPubKey": "0x136ef6be87de9c925869387782afb4cf19496999c2684709daeb3af8d0b59d800bbe05870789f0f9b3cadababa69f5a00a38bbcba71d99c4c35d671442232c4d3017fd6b99e8356a3e4e985bdfc60bbcb8d939c87976a1ff677d7c42989b379a0b4c0f168a544c892bd2b3ec480e3d6c58c7dddb8d83677ebee2e87ab3660b80",
      "BLSG1PubKey": "0x14d44a97d2fc3ea62b6dcf2bd857079bd261993152f11aef5dd001db68b20d2d1ba45f117b6530a7aec45d7d90fd4e15d2a62f62b706eaa115aa801caeee294b",
      "BLSProofOfPossession": "0x15933821ab5c324c95ba46ad99349e4c0d068a290fabd90f59eee9e69597ce012e4df0614f48696ff22c52a0667ee1daf8ca581d920c39ba726f0f3c69cae66e"
    },
    {
      "Address": "0xdC757c8e3b8800d977a34f802131FAA870d264c4",
      "SignerAddress": "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF",
      "ECDSASignature": "0x9b4f29d54a31b052fc9fcfdc968912a59b308df021f32b3c923438c0d65ff2cc47b8d01bf5968d70a8c82875a1852c313540f89467a3fe833902a0c0aff5a27a01",
      "PublicKeyHex": "0x04e7678fb997c00d5998f79413d73ebde98865cd0d7fa82e2ab6d0920a72204d8c49c14f873ec9ee0e0b38651001acc9a4c1a0a63de6c6589b896f21f6a6bb6837",
      "BLSPubKey": "0x0a2e37ecad6e69bfec9fec2b345d0f8441a0f63acf8b45c0131a78e5d777d52e0a39404ca85f2c08752c1d4ff8df05c82c7880779d61fe3fabcd4fd682463c0515b1f0217561a6a72bd381da19e34c5560c6eccb08ff83d7d3f4ac6da7f5d1ed15a2780f782c1fa571fa65b99694af559b9df168b1d8745ac3bbc7d3fe550b94",
      "BLSG1PubKey": "0x15b7bcf0accf839170a5d4621282edcf14f4a438f8e53abcead5f0528cb91cb1135fd4e82ede1493ab1209af122e1dc186c885cc96d2413cbc09a58163b91eb9",
      "BLSProofOfPossession": "0x2cfa8f2daa103964dfa380bfc30e695b94ef62832e1e641d9febf4ab78b128392f2e33612a66cc75e35a6326b8a6aa0be26fd9567d4aeff16e9883a671648b5d"
    },
    {
      "Address": "0x257Cc34FB139A2db4Da96496Be03358d89e52d95",
      "SignerAddress": "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11",
      "ECDSASignature": "0x303ea4fdbd348c32655e7a460802be169f4bc4ca1a7030af941de4a57069dfb5253c0c087e06e258f63e50176bd5b54bf462514d8116ec3394bdc875c5678f7500",
      "PublicKeyHex": "0x04ec7664543f2dae218176a072ca7bfc16632438793077c06cf05975cc1302ee60c27f29e2cc3b64ffbaa69d2939e937f99a7bf93d7c5fa59bffbcd769e4f234e8",
      "BLSPubKey": "0x086fac850f3a9f36e8a5107eab0ba79044043dc2cc6b897cbbd0d4bf805570ff270a98f28e2d2e70b7b2ecc41a4a13e453178354997aa2038852c5945f0564bb02cdf57642881a1b40417fe3620429fc087f8dee6a68e5d7193d3243c38a1f3827d0f4cb616722a1fa78a283a17589d7688a769ade77e9d6417c6e2a9adf59c3",
      "BLSG1PubKey": "0x2fd433e93187f6b3d15664ec48073bd73d57c801c4a8bfc1e0e3abd3deefc45619d45ac7ad54df7dda5b8afd6f882c9d9f879dbc6d587f1da5da1751baac729f",
      "BLSProofOfPossession": "0x1ee63371c158365acde92ee1a53ca1dcb54efb52d1c077735dcd46237c7938b62720e4903dd9b825f3bed819422791352a9b7fe1a39e1183d33c0ca892cff1f4"
    },
    {
      "Address": "0xac146d6629F8C3B8F2e830275B583C5402032472",
      "SignerAddress": "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5",
      "ECDSASignature": "0x29388d40ee2ee4468fca04b624c77208b7b00c56c532fac53314cf39e3e8feb80c6bd2b7d25a6e20e9da0adaeec4b49c13b673e6ca2d088370e0b6189fb216f000",
      "PublicKeyHex": "0x04ef2af91ba2fc2b04bc47c7d59d6d07a0dea2a62c5b537d4a83a387bee44245317de753c4e45858708c0d31473c6595ac9dddbcf7ac02a13df4af1a188e2c9c24",
      "BLSPubKey": "0x03fea7bc386ea24aaa19c563a4f26f38cbc2ce172ba2310587405f4f05777fb911a4c3553b7b6529ea02a9da3ae2df6f70c3409105b39e1930d6a6ae8344fc221f5dfb2e73cc8ce434d1af33d95366796bdec26ca7cfcc0a03867fabf471884206db6b9e175a131995bd0c70b93a6f2eec96d831ad0c42d13d334f780d578834",
      "BLSG1PubKey": "0x1b037f39d9f8e74b608a898249cc3d156ff1f0051026388366b85a84aac43bb4068275cd909e16b29f1b3bc97e91ec0a8b95a11b8a574cbc2c9ea142d26c8a49",
      "BLSProofOfPossession": "0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402"
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
./atlas --datadir ./node-1 --networkid 110112 --port 20201 --mine --miner.validator 0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C --unlock 0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C console
./atlas --datadir ./node-2 --networkid 110112 --port 20202 --mine --miner.validator 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d --unlock 0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d console
./atlas --datadir ./node-3 --networkid 110112 --port 20203 --mine --miner.validator 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 --unlock 0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252 console
./atlas --datadir ./node-4 --networkid 110112 --port 20204 --mine --miner.validator 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 --unlock 0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03 console
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
./atlas --datadir ./node-rpc init ./genesis.json

./atlas --datadir ./node-rpc --networkid 110112 --port 20205 --http --http.addr "0.0.0.0" --http.port 7445 --http.api eth,web3,net,debug,txpool,header,istanbul --http.corsdomain "*" console
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
INFO [03-16|20:21:27.683] Looking for peers                        peercount=2 tried=5 static=4
INFO [03-16|20:21:37.582] Reset timer to resend RoundChange msg    address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 func=resetResendRoundChangeTi
mer cur_seq=23 cur_epoch=1 cur_round=0 des_round=5 state="Waiting for new round" address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 timeout=17.5s
INFO [03-16|20:21:39.311] Looking for peers                        peercount=3 tried=0 static=4
```

