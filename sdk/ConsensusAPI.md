# Validator-Tool-RPC

Get information on the chain about validators.

## GetSnapshot

retrieves the state snapshot at a given block.

##### Parameters
`QUANTITY|TAG` - hexadecimal  of a block number


##### Returns

`epoch` - The epoch number of the block number.

`number` - The number of the block number.

`validators` - validator`s information of the epoch.




##### example

```shell

# request:

curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getSnapshot","params":["0x1"],"id":1}' http://192.168.10.201:8545 
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "epoch": 20,
    "number": 76,
    "hash": "0xa599dec7072503e480da203bca5fc42ad9c9399bdb59034d2aee07bc092cb252",
    "validators": [
      {
        "Address": "0x1c0edab88dbb72b119039c4d14b1663525b3ac15",
        "BLSPublicKey": "0x136ef6be87de9c925869387782afb4cf19496999c2684709daeb3af8d0b59d800bbe05870789f0f9b3cadababa69f5a00a38bbcba71d99c4c35d671442232c4d3017fd6b99e8356a3e4e985bdfc60bbcb8d939c87976a1ff677d7c42989b379a0b4c0f168a544c892bd2b3ec480e3d6c58c7dddb8d83677ebee2e87ab3660b8000",
        "BLSG1PublicKey": "0x14d44a97d2fc3ea62b6dcf2bd857079bd261993152f11aef5dd001db68b20d2d1ba45f117b6530a7aec45d7d90fd4e15d2a62f62b706eaa115aa801caeee294b0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "UncompressedBLSPublicKey": "E272vofenJJYaTh3gq+0zxlJaZnCaEcJ2us6+NC1nYALvgWHB4nw+bPK2rq6afWgCji7y6cdmcTDXWcUQiMsTTAX/WuZ6DVqPk6YW9/GC7y42TnIeXah/2d9fEKYmzeaC0wPFopUTIkr0rPsSA49bFjH3duNg2d+vuLoerNmC4A="
      },
      {
        "Address": "0x16fdbcac4d4cc24dca47b9b80f58155a551ca2af",
        "BLSPublicKey": "0x0a2e37ecad6e69bfec9fec2b345d0f8441a0f63acf8b45c0131a78e5d777d52e0a39404ca85f2c08752c1d4ff8df05c82c7880779d61fe3fabcd4fd682463c0515b1f0217561a6a72bd381da19e34c5560c6eccb08ff83d7d3f4ac6da7f5d1ed15a2780f782c1fa571fa65b99694af559b9df168b1d8745ac3bbc7d3fe550b9400",
        "BLSG1PublicKey": "0x15b7bcf0accf839170a5d4621282edcf14f4a438f8e53abcead5f0528cb91cb1135fd4e82ede1493ab1209af122e1dc186c885cc96d2413cbc09a58163b91eb90000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "UncompressedBLSPublicKey": "Ci437K1uab/sn+wrNF0PhEGg9jrPi0XAExp45dd31S4KOUBMqF8sCHUsHU/43wXILHiAd51h/j+rzU/WgkY8BRWx8CF1YaanK9OB2hnjTFVgxuzLCP+D19P0rG2n9dHtFaJ4D3gsH6Vx+mW5lpSvVZud8Wix2HRaw7vH0/5VC5Q="
      },
      {
        "Address": "0x2dc45799000ab08e60b7441c36fcc74060ccbe11",
        "BLSPublicKey": "0x086fac850f3a9f36e8a5107eab0ba79044043dc2cc6b897cbbd0d4bf805570ff270a98f28e2d2e70b7b2ecc41a4a13e453178354997aa2038852c5945f0564bb02cdf57642881a1b40417fe3620429fc087f8dee6a68e5d7193d3243c38a1f3827d0f4cb616722a1fa78a283a17589d7688a769ade77e9d6417c6e2a9adf59c300",
        "BLSG1PublicKey": "0x2fd433e93187f6b3d15664ec48073bd73d57c801c4a8bfc1e0e3abd3deefc45619d45ac7ad54df7dda5b8afd6f882c9d9f879dbc6d587f1da5da1751baac729f0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "UncompressedBLSPublicKey": "CG+shQ86nzbopRB+qwunkEQEPcLMa4l8u9DUv4BVcP8nCpjyji0ucLey7MQaShPkUxeDVJl6ogOIUsWUXwVkuwLN9XZCiBobQEF/42IEKfwIf43uamjl1xk9MkPDih84J9D0y2FnIqH6eKKDoXWJ12iKdpred+nWQXxuKprfWcM="
      },
      {
        "Address": "0x6c5938b49bacde73a8db7c3a7da208846898bff5",
        "BLSPublicKey": "0x03fea7bc386ea24aaa19c563a4f26f38cbc2ce172ba2310587405f4f05777fb911a4c3553b7b6529ea02a9da3ae2df6f70c3409105b39e1930d6a6ae8344fc221f5dfb2e73cc8ce434d1af33d95366796bdec26ca7cfcc0a03867fabf471884206db6b9e175a131995bd0c70b93a6f2eec96d831ad0c42d13d334f780d57883400",
        "BLSG1PublicKey": "0x1b037f39d9f8e74b608a898249cc3d156ff1f0051026388366b85a84aac43bb4068275cd909e16b29f1b3bc97e91ec0a8b95a11b8a574cbc2c9ea142d26c8a490000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "UncompressedBLSPublicKey": "A/6nvDhuokqqGcVjpPJvOMvCzhcrojEFh0BfTwV3f7kRpMNVO3tlKeoCqdo64t9vcMNAkQWznhkw1qaug0T8Ih9d+y5zzIzkNNGvM9lTZnlr3sJsp8/MCgOGf6v0cYhCBttrnhdaExmVvQxwuTpvLuyW2DGtDELRPTNPeA1XiDQ="
      }
    ]
  }
}

```

## GetValidators

retrieves the list validators that must sign a given block.

##### Parameters
`QUANTITY|TAG` - hexadecimal  of a block number


##### Returns

address of these validators 

##### example

```shell

# request:

curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":["0x1"],"id":1}' http://192.168.10.201:8545 
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    "0x1c0edab88dbb72b119039c4d14b1663525b3ac15",
    "0x16fdbcac4d4cc24dca47b9b80f58155a551ca2af",
    "0x2dc45799000ab08e60b7441c36fcc74060ccbe11",
    "0x6c5938b49bacde73a8db7c3a7da208846898bff5"
  ]
}

```

## GetValidatorsBLSPublicKeys

retrieves the list of validators BLS public keys that must sign a given block.

##### Parameters
`QUANTITY|TAG` - hexadecimal  of a block number


##### Returns

blsPublicKeys of these validators

##### example

```shell

# request:

curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getValidatorsBLSPublicKeys","params":["0x1"],"id":1}' http://192.168.10.201:8545 
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    "0xbe77f945929d5dd3fe99aa825df0f5b1e8ea11786333b4492a8624a4d08dcee0e89df327359e8ec3f2d8ae01e938b7003414aa2d6523ffa02fde42b278cbae311fd39f1fbcad8e3188442ea31dee662389599751f8e73b99215cefc2e0003f81",
    "0x4f38a71fb13ab20f7bbfc2749ab15d775b7729842d967ca4f4115d1fcb3f378c892d073344f84e2abd8995a16eeee8004f4e588c30261e08a5dae70c581f904ea86b574bfe279222cf6b7913bebb0d3bd6c2bbe2e2ea1d338f145c4d95b99201",
    "0x8cf3bfcbfc76e9a99b70cad65ae51f8a8972e3e230445a55c8cf6b96dea7a2d0d970e3545e1316554d5d3b0a53582800ad4de92e3b06b62aa6f7677fdc2885a90b75fd80e2db2775512d8f3d3900aabae5b0525786d65615994b07afe7f69481",
    "0x1bbb8eb14a7f5dddc9de3356ce4247dab8e554fa83cd33e663db148b5d2dd14485f090978c84074154b450329de06b018eac04113ede1eedadf891ee862877af92a648c162be62182db90e8c83f8fd154fc14f13676bcb1fe3503260b6261a01"
  ]
}

```

## GetProposer

GetProposer retrieves the proposer for a given block number (i.e. sequence) and round.

##### Parameters
- `sequence`  - hexadecimal  of a block number.
- `round`     - The Number of rotations
 

##### Returns

proposer address of this block number

##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getProposer","params":["0x21",0],"id":1}' http://192.168.10.201:8545
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x6c5938b49bacde73a8db7c3a7da208846898bff5"
}

```

## IsValidating

returns true if this node is participating in the consensus protocol.

##### Parameters
none


##### Returns

bool value about this node is participating in the consensus protocol.

##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_isValidating","id":1}' http://192.168.10.201:8545
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": false
}
```

## GetLookbackWindow

GetLookbackWindow retrieves A fixed value about lookbackWindow check whether the validator has signed in a fixed interval.

##### Parameters
none

##### Returns
 
interval value about lookbackWindow .


##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getLookbackWindow","params":["0x1"],"id":1}' http://192.168.10.201:8545
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": 12
}
```

## GetValEnodeTable

Retrieve the Validator Enode Table.

##### Parameters
none

##### Returns

validators Enode Table


##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getValEnodeTable","id":1}' http://192.168.10.201:8545
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF": {
      "publicKey": "0x03e7678fb997c00d5998f79413d73ebde98865cd0d7fa82e2ab6d0920a72204d8c",
      "enode": "enode://7c5b11c810839a9a68306f701790cdf04bf34cfeead6f02dbc6139290a9175cc2ce535b59d7d1829c7cb3edba395871667f1ba0af0441945e3679be36ff5ff7f@127.0.0.1:31001",
      "version": 1646035550,
      "highestKnownVersion": 1646035550,
      "numQueryAttemptsForHKVersion": 0,
      "lastQueryTimestamp": "2022-02-28 16:07:35.1808733 +0800 CST"
    },
    "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11": {
      "publicKey": "0x02ec7664543f2dae218176a072ca7bfc16632438793077c06cf05975cc1302ee60",
      "enode": "enode://181cf6e375f502137074b65e8ba339eb7be85b430777bb267a9db96772dfb8182b684b2e8228464e99c22b31934ddad4b15d6709cfe667d740a0cbe07d3ac482@127.0.0.1:31002",
      "version": 1646035556,
      "highestKnownVersion": 1646035556,
      "numQueryAttemptsForHKVersion": 0,
      "lastQueryTimestamp": "2022-02-28 16:06:35.1831069 +0800 CST"
    },
    "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5": {
      "publicKey": "0x02ef2af91ba2fc2b04bc47c7d59d6d07a0dea2a62c5b537d4a83a387bee4424531",
      "enode": "enode://ec7664543f2dae218176a072ca7bfc16632438793077c06cf05975cc1302ee60c27f29e2cc3b64ffbaa69d2939e937f99a7bf93d7c5fa59bffbcd769e4f234e8@127.0.0.1:31003",
      "version": 1646035561,
      "highestKnownVersion": 1646035561,
      "numQueryAttemptsForHKVersion": 0,
      "lastQueryTimestamp": "2022-02-28 16:07:35.1808733 +0800 CST"
    }
  }
}
```

## GetVersionCertificateTableInfo

Retrieve the Validator Signature timestamp.

##### Parameters
none


##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getVersionCertificateTableInfo","id":1}' http://192.168.10.201:8545
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF": {
      "address": "0x16FdBcAC4D4Cc24DCa47B9b80f58155a551ca2aF",
      "version": 1646035850
    },
    "0x1c0eDab88dbb72B119039c4d14b1663525b3aC15": {
      "address": "0x1c0eDab88dbb72B119039c4d14b1663525b3aC15",
      "version": 1646035835
    },
    "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11": {
      "address": "0x2dC45799000ab08E60b7441c36fCC74060Ccbe11",
      "version": 1646035856
    },
    "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5": {
      "address": "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5",
      "version": 1646035861
    }
  }
}
```

## GetCurrentRoundState

retrieves the current IBFT RoundState

##### Parameters
none


##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getCurrentRoundState","id":1}' http://192.168.10.201:8545
# RESPONSE:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "state": "Accept request",
    "sequence": 382,
    "round": 0,
    "desiredRound": 0,
    "pendingRequestHash": null,
    "validatorSet": [
      "0x1c0edab88dbb72b119039c4d14b1663525b3ac15",
      "0x16fdbcac4d4cc24dca47b9b80f58155a551ca2af",
      "0x2dc45799000ab08e60b7441c36fcc74060ccbe11",
      "0x6c5938b49bacde73a8db7c3a7da208846898bff5"
    ],
    "proposer": "0x16fdbcac4d4cc24dca47b9b80f58155a551ca2af",
    "prepares": [],
    "commits": [],
    "parentCommits": [
      "0x2dc45799000ab08e60b7441c36fcc74060ccbe11",
      "0x16fdbcac4d4cc24dca47b9b80f58155a551ca2af",
      "0x1c0edab88dbb72b119039c4d14b1663525b3ac15",
      "0x6c5938b49bacde73a8db7c3a7da208846898bff5"
    ],
    "preprepare": null,
    "preparedCertificate": null
  }
}

```

## ForceRoundChange

Force current node timeout

##### Parameters
none

##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_forceRoundChange","id":1}' http://192.168.10.201:8545
# RESPONSE:
{"jsonrpc":"2.0","id":1,"result":true}
```  

## GetCurrentReplicaState

retrieves the current replica state

##### Parameters
none

##### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getCurrentReplicaState","id":1}' http://192.168.10.201:8545
# RESPONSE:
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "state": "Primary",
    "isPrimary": true,
    "startValidatingBlock": null,
    "stopValidatingBlock": null
  }
}
```
