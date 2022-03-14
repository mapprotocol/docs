# Validator-Tool-RPC

Get information on the chain about validators.

## GetSnapshot

retrieves the state snapshot at a given block.

### example

```shell

# request:

curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getSnapshot","params":["0x1"],"id":1}' http://192.168.10.201:8545 
# response:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "epoch": 20,
    "number": 1,
    "hash": "0x12c69199c03a4d1e5607d08a9b5dd84acb543855d5a691ecb91c4bfea1d89fe9",
    "validators": [
      {
        "Address": "0x1c0edab88dbb72b119039c4d14b1663525b3ac15",
        "BLSPublicKey": "0xbe77f945929d5dd3fe99aa825df0f5b1e8ea11786333b4492a8624a4d08dcee0e89df327359e8ec3f2d8ae01e938b7003414aa2d6523ffa02fde42b278cbae311fd39f1fbcad8e3188442ea31dee662389599751f8e73b99215cefc2e0003f81",
        "UncompressedBLSPublicKey": "vnf5RZKdXdP+maqCXfD1sejqEXhjM7RJKoYkpNCNzuDonfMnNZ6Ow/LYrgHpOLcANBSqLWUj/6Av3kKyeMuuMR/Tnx+8rY4xiEQuox3uZiOJWZdR+Oc7mSFc78LgAD8BlkSdzKrlvWuiShgIDoPyrkAPj8FBBbfmM+6+faVlf12Z6eoWLPCJpLqlSAKawDEBuCxkwzUMYsdQhMWfvrDglTmhzbyhnQgVOSCYSECBRE31H2SP98WkvgdiyP5eRY0B"
      },
      {
        "Address": "0x16fdbcac4d4cc24dca47b9b80f58155a551ca2af",
        "BLSPublicKey": "0x4f38a71fb13ab20f7bbfc2749ab15d775b7729842d967ca4f4115d1fcb3f378c892d073344f84e2abd8995a16eeee8004f4e588c30261e08a5dae70c581f904ea86b574bfe279222cf6b7913bebb0d3bd6c2bbe2e2ea1d338f145c4d95b99201",
        "UncompressedBLSPublicKey": "TzinH7E6sg97v8J0mrFdd1t3KYQtlnyk9BFdH8s/N4yJLQczRPhOKr2JlaFu7ugAT05YjDAmHgil2ucMWB+QTqhrV0v+J5Iiz2t5E767DTvWwrvi4uodM48UXE2VuZIBcRSScPehdY5CfM+Z2IJG1+3W1XctINNxTXa7FIWnT5SOgolR+TuGX+6KfH6fEQMAhB1k+W21F+2rRvfAMoPASFM6Bsd/HgsSU8FMwiEGYY0QfuNYItQVOW5Yw1SAQLEA"
      },
      {
        "Address": "0x2dc45799000ab08e60b7441c36fcc74060ccbe11",
        "BLSPublicKey": "0x8cf3bfcbfc76e9a99b70cad65ae51f8a8972e3e230445a55c8cf6b96dea7a2d0d970e3545e1316554d5d3b0a53582800ad4de92e3b06b62aa6f7677fdc2885a90b75fd80e2db2775512d8f3d3900aabae5b0525786d65615994b07afe7f69481",
        "UncompressedBLSPublicKey": "jPO/y/x26ambcMrWWuUfioly4+IwRFpVyM9rlt6notDZcONUXhMWVU1dOwpTWCgArU3pLjsGtiqm92d/3CiFqQt1/YDi2yd1US2PPTkAqrrlsFJXhtZWFZlLB6/n9pQBAT1ftvRFprsH+oAAnx51ZmyLEywKFFHDk0QxSF/ZmxOpIwzhQiVNSMK9k9ygFYgAHuzpLp7qv2tvNSJdAlTNlE+b8X7ZR3xWNzw+LJXdK6XnXFGmU00AZyKoEoNen1UB"
      },
      {
        "Address": "0x6c5938b49bacde73a8db7c3a7da208846898bff5",
        "BLSPublicKey": "0x1bbb8eb14a7f5dddc9de3356ce4247dab8e554fa83cd33e663db148b5d2dd14485f090978c84074154b450329de06b018eac04113ede1eedadf891ee862877af92a648c162be62182db90e8c83f8fd154fc14f13676bcb1fe3503260b6261a01",
        "UncompressedBLSPublicKey": "G7uOsUp/Xd3J3jNWzkJH2rjlVPqDzTPmY9sUi10t0USF8JCXjIQHQVS0UDKd4GsBjqwEET7eHu2t+JHuhih3r5KmSMFivmIYLbkOjIP4/RVPwU8TZ2vLH+NQMmC2JhoBRiTJN+lnQ0H2/re07VcOulQKOiukLaqMjmHQyqDJ+y4ymENPlUzpOIJnAva5f5EAeEkwQBzNkb/rGZofPb5is/zidskNwgdLVDBQHl6jO3hP60fkMidmFkzz394GBGoA"
      }
    ]
  }
}

```

## GetValidators

retrieves the list validators that must sign a given block.

### example

```shell

# request:

curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getValidators","params":["0x1"],"id":1}' http://192.168.10.201:8545 
# response:

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

### example

```shell

# request:

curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getValidatorsBLSPublicKeys","params":["0x1"],"id":1}' http://192.168.10.201:8545 
# response:

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

retrieves the list of validators BLS public keys that must sign a given block.

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getProposer","params":["0x21",0],"id":1}' http://192.168.10.201:8545
# response:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": "0x6c5938b49bacde73a8db7c3a7da208846898bff5"
}

```

## IsValidating

returns true if this node is participating in the consensus protocol.

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_isValidating","id":1}' http://192.168.10.201:8545
# response:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": false
}
```

## GetLookbackWindow

returns true if this node is participating in the consensus protocol.

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getLookbackWindow","params":["0x1"],"id":1}' http://192.168.10.201:8545
# response:

{
  "jsonrpc": "2.0",
  "id": 1,
  "result": 12
}
```

## GetValEnodeTable

Retrieve the Validator Enode Table.

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getValEnodeTable","id":1}' http://192.168.10.201:8545
# response:

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

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getVersionCertificateTableInfo","id":1}' http://192.168.10.201:8545
# response:

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

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getCurrentRoundState","id":1}' http://192.168.10.201:8545
# response:

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

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_forceRoundChange","id":1}' http://192.168.10.201:8545
# response:
{"jsonrpc":"2.0","id":1,"result":true}
```  

## GetCurrentReplicaState

retrieves the current replica state

### example

```shell

# request:
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"istanbul_getCurrentReplicaState","id":1}' http://192.168.10.201:8545
# response:
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
