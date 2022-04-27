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
PublicKeyHex:   0x040456ddacfeecf28a9fed43a1d5d3a147c84a2024270fa76ac6d1886ef87e4ae04f5c719b4b98e1c3b135e0d012134ddc31881d08acc210ff203c18653633d36e
BLS Public address of the key:   0x09d635d808a637f50dd5d90bbe2c5c750ff69e8422bde26cbe57eb095741dcb122ad53b16ce611304fb25bdf027fe996ce7aabbefb0e0cdc8ecde878929583ba1131c21d5ac81a4b299a3a11da0752773ae6c07214888f2ab4eacd9e9aa428242a53fcc07697a75a04489a35cef22f028d15958a53439035c46edca443d1e3a200
BLSProofOfPossession:   0x15f238946bae1fdfa77e1b9b9fde8f4f4fe54195f697b18f4a17196309ed234204cd5c621b4ae3d4347024e319afce035a2a1e5197881141b6dfde2785235141
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
Fill in the information we output when we created the account into the corresponding key value.
The value corresponding to the `AdminAddress` key can be any of the four accounts we created above or other accounts.

Just like the following:

```json
{
  "AdminAddress": "0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C",
  "Validators": [
    {
      "Address": "0xF930B74D2b1b703B879ab54E225ECc18Ab28e61C",
      "PublicKeyHex": "0x040456ddacfeecf28a9fed43a1d5d3a147c84a2024270fa76ac6d1886ef87e4ae04f5c719b4b98e1c3b135e0d012134ddc31881d08acc210ff203c18653633d36e",
      "BLSPubKey": "0x09d635d808a637f50dd5d90bbe2c5c750ff69e8422bde26cbe57eb095741dcb122ad53b16ce611304fb25bdf027fe996ce7aabbefb0e0cdc8ecde878929583ba1131c21d5ac81a4b299a3a11da0752773ae6c07214888f2ab4eacd9e9aa428242a53fcc07697a75a04489a35cef22f028d15958a53439035c46edca443d1e3a200",
      "BLSG1PubKey": "0x09d635d808a637f50dd5d90bbe2c5c750ff69e8422bde26cbe57eb095741dcb122ad53b16ce611304fb25bdf027fe996ce7aabbefb0e0cdc8ecde878929583ba1131c21d5ac81a4b299a3a11da0752773ae6c07214888f2ab4eacd9e9aa428242a53fcc07697a75a04489a35cef22f028d15958a53439035c46edca443d1e3a200",
      "BLSProofOfPossession": "0x15f238946bae1fdfa77e1b9b9fde8f4f4fe54195f697b18f4a17196309ed234204cd5c621b4ae3d4347024e319afce035a2a1e5197881141b6dfde2785235141"
    },
    {
      "Address": "0x0e2699Be2B47Dfd7560c4771A32A50b64F81293d",
      "PublicKeyHex": "0x04f47a1376fff0c026f63f539d7208d4f9e7d046d4e3c49016333beb2bb9517532a041f652a714c7971fc2ff5551752ed958dc3a54504bea956969fd05dcad7ade",
      "BLSPubKey": "0x0fe202e9adfff4c5748235236c1a6de404a69085ed89c7ba5a68bd98c83dc07f17b6b4d0edaef436f5fb4a97824e4aa2e5d3d42ac8189ea15fcafc34f69cf7e3056b668d73b4ca90860ff15f9b4b6807e0089cc96921d91c0b219746f4016f3d01789b5fd2ff8f8cbe5ccb0af6cace7f210d1f73e836b8e25601afdbefd19a0600",
      "BLSG1PubKey": "0x0fe202e9adfff4c5748235236c1a6de404a69085ed89c7ba5a68bd98c83dc07f17b6b4d0edaef436f5fb4a97824e4aa2e5d3d42ac8189ea15fcafc34f69cf7e3056b668d73b4ca90860ff15f9b4b6807e0089cc96921d91c0b219746f4016f3d01789b5fd2ff8f8cbe5ccb0af6cace7f210d1f73e836b8e25601afdbefd19a0600",
      "BLSProofOfPossession": "0x0ba85160de008a330397c3efb886335b7b00a5689a0db74c6a224ce7965ff9890131fd80e474b6d16b64cc2b99deddaaa6858ab2e6f6e8f7ff1035292b78bf1a"
    },
    {
      "Address": "0x3c0ef282c8c62a44eA2FE8928b6bd89a16fD8252",
      "PublicKeyHex": "0x0413ac8c3aa0b5bca1768fc6b631503d7ef28f84794bb68404c46187eb7bbfc5360123da2fc76b223ff34451fbd204369a30de1dfc421946b708629911fd9395f0",
      "BLSPubKey": "0x19afff51ccfb65f91fe8a8922f072897c657d41823b8a87ac296b42bbeaa1969292f5c0cbd6991b083dcb95ac4c4c2961ad93bc1debc325bfdced44430b5d9da291ec44382e494e73ed6faaf5a9047fa23986f1a904166d5b05aa4e7a7e77fc9030d7acbf12bc3f4d27028779fd785c06096ab863f6747d2e63fec5bdc3aca1800",
      "BLSG1PubKey": "0x19afff51ccfb65f91fe8a8922f072897c657d41823b8a87ac296b42bbeaa1969292f5c0cbd6991b083dcb95ac4c4c2961ad93bc1debc325bfdced44430b5d9da291ec44382e494e73ed6faaf5a9047fa23986f1a904166d5b05aa4e7a7e77fc9030d7acbf12bc3f4d27028779fd785c06096ab863f6747d2e63fec5bdc3aca1800",
      "BLSProofOfPossession": "0x0a24d98df93e65c87c42d71f7ce8aeb3b8a580d74382dfd824c4c6b64df60cff3040b8c5979a68529d05a5e357337314fa63a655a3b0eb26f7a0f385488a46c2"
    },
    {
      "Address": "0x41E4A55Ef06c1961B3e143357e24cA7f92e4DD03",
      "PublicKeyHex": "0x040e36f52adb9da0e98afcc889eff6a20c0266c1b083db5eb234c0d18ac1d2226397862599951623aab0cd3dc6fdf43974a3baa69edbe0a5c01762782b6be28900",
      "BLSPubKey": "0x0832b7c57c144b1e09a51b244a105b1cbc6932f9257f97365403a93f9f695a42178641aa1b6389acf294ba0e3f949ea592f2ef0d7ab6c2813e661049107b222503c117795d7a54f3e555b67e3ba1626a35f498ce58d168962ee8b366580b74aa0d042e6459265880dbbcf76d10c31cbd38e344a7fa02a1b480ac651ed46146ff00",
      "BLSG1PubKey": "0x0832b7c57c144b1e09a51b244a105b1cbc6932f9257f97365403a93f9f695a42178641aa1b6389acf294ba0e3f949ea592f2ef0d7ab6c2813e661049107b222503c117795d7a54f3e555b67e3ba1626a35f498ce58d168962ee8b366580b74aa0d042e6459265880dbbcf76d10c31cbd38e344a7fa02a1b480ac651ed46146ff00",
      "BLSProofOfPossession": "0x2ff772a97bf2ca4aaba9e31b52375b00c3bb732efe2f84fab8ed32c74d9189b018c6c8079066614a1e65c7def0f08d4f60cd5467b129316e33791e58f021f44a"
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
INFO [03-16|20:21:27.683] Looking for peers                        peercount=2 tried=5 static=4
INFO [03-16|20:21:37.582] Reset timer to resend RoundChange msg    address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 func=resetResendRoundChangeTi
mer cur_seq=23 cur_epoch=1 cur_round=0 des_round=5 state="Waiting for new round" address=0x2dC45799000ab08E60b7441c36fCC74060Ccbe11 timeout=17.5s
INFO [03-16|20:21:39.311] Looking for peers                        peercount=3 tried=0 static=4
```

