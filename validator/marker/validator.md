关于 validator 的注册、注销等介绍。

## register

Register a new validator. Through this command, we will transfer your Commission, ecdsaPublicKey, blsPublicKey,
blsG1PublicKey, and BLSProof to the management contract to manage and secure your assets. Your ecdsaPublicKey,
blsPublicKey, and BLSProof will be obtained through the specified keystore. The ECDSA public key that the validator is
using for consensus should match the validator signer and be 64 bytes in length. The BLS public key that the validator
is using for consensus should pass proof of possession and be 129 bytes in length. The BLS G1 public key that the
validator is using for consensus should be 129 bytes in length. The BLS public key proof-of-possession consists of a
signature on the account address and is 129 bytes in length.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC
  service address.
- `keystore`: The path to the keystore file.
- `commission`: The commission rate for the validator's rewards. The commission parameter is set relative to 1000000,
  with a range from 0 to 1000000. If you want to set the commission rate to 15%, you need to set this parameter to
  150000 (150000/1000000=15%). This attribute is one of the reference objects used by voters when voting.

```shell
./marker register
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--commission 0.1

RESPONSE:
success
or
Failed
```

## quicklyRegister

To quickly register a new validator, you can use the `quicklyRegister` command, which integrates
the `createAccount`, `lockedMAP`, and `register` commands.

Please note that you can only use this command once. Regardless of the success or failure of the command, it includes
the operations corresponding to the `createAccount` and `lockedMAP` commands and does not have the feature of being
reusable.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC
  service address.
- `keystore`: The path to the keystore file.
- `commission`: The commission rate for the validator's rewards. The commission parameter is set relative to 1000000,
  with a range from 0 to 1000000. If you want to set the commission rate to 15%, you need to set this parameter to
  150000 (150000/1000000=15%). This attribute is one of the reference objects used by voters when voting.
- `lockedNum`: The amount of MAPO tokens to be locked in order to register as a validator.
- `signerPriv`: The private key of the signer account.

```shell
./marker quicklyRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--commission 100000
--signerPriv 842e1d8a93b4e46104da96676066ddb0973c63ec80a15746856c046dd4a1004c
--lockedNum 1000000

RESPONSE:
success
or
Failed
```

## deregister

The `deregister` command is used to unregister a validator. You must be a validator to use this command.

The contract has a minimum time requirement to become a validator (default is 60 days). To deregister a validator, you
must have exceeded this time.

To prevent malicious resource occupation during the deregistration process, we will put your deregistration request in a
pending state and execute batch deregistration at the last block of the epoch.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC
  service address.
- `keystore`: The path to the keystore file.

```shell
./marker deregister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15


RESPONSE:
success
or
Failed
```

## revertRegister

If you deregistered your account in a specific epoch, you can use the `revertRegister` command within the same epoch to
restore your validator status.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.

```shell
./marker revertRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15


RESPONSE:
success
or
Failed
```

## authorizeValidatorSigner

Call this method before becoming a validator.

If you need to authorize another account to perform on-chain consensus operations instead of using the validator
account, you can call this method to grant authorization. This allows another account to perform on-chain consensus
operations.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.
- `signerPriv`: The private key of the signer account.

```shell
./marker authorizeValidatorSigner
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--signerPriv   842e1d8a93b4e46104da96676066ddb0973c63ec80a15746856c046dd4a1004c   

RESPONSE:
INFO [05-30|13:41:55.131] === authorizeValidatorSigner === 
INFO [05-30|13:41:55.140] TxInfo                                   func=sendContractTransaction  TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =214
INFO [05-30|13:41:55.141] Please waiting                           func=getResult                 txHash =0x6ccc9758199c105c2b8eb9f9807b553baaa9062ec871a1fc47f3866a4a1e8d49
INFO [05-30|13:42:03.231] Transaction Success                      func=queryTx                  block Number=37

```

## makeECDSASignatureFromSigner

Generate an ECDSA signature signed by the signer account, which has signed the validator account.

Parameter description:

- `signerPriv`: The private key of the signer account.
- `target`: The address of the validator account you want to sign.

```shell
makeECDSASignatureFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--target
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:41:55.131] === ECDSASignature === 
INFO [05-30|13:41:55.131] === signer ===                           account=0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d
INFO [05-30|13:41:55.131] ECDSASignature                           result=0x6dcec34a67a3388b6fbf93ad48f88aa88c0ce46789de0f9e042acbe6e116c97f26d645652576a65602ed97f7ca16f890898b8e761de3edc34447edb2e41713ad01

```

## makeBLSProofOfPossessionFromSigner

Generate a BLSProofOfPossession signed by the signer account for the validator account.

Parameter description:

- `signerPriv`: The private key of the signer account.
- `target`: The address of the validator account you want to sign.

```shell
makeBLSProofOfPossessionFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--target
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:52:40.371] === makeBLSProofOfPossessionFromSigner === 
INFO [05-30|13:52:40.375] === pop ===                                result=0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402

```

## signerToAccount

Query the validator account that has authorized the target signer account.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path to the keystore file.
- `target`: The address of the signer account.

```shell
signerToAccount
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2022-05-11T03-31-08.562982000Z--6621f2b6da2bed64b5ffbd6c5b2138547f44c8f9
--target "0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d"

RESPONSE:
INFO [05-30|13:52:40.371] === signerToAccount === 
INFO [05-30|13:56:05.126] signerToAccount                          authorizingAccount=0xa88cF1842AC49A22435D6B4e4c1d8782e3C29EfE
```

## generateSignerProof

Generate a proof using the signer private key.

Parameter description:

- `validator`: The address of the validator account.
- `signerPriv`: The private key of the signer account.

```shell
./marker generateSignerProof 
--validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 
--signerPriv 040939e5...604b6f25

RESPONSE:
INFO [08-26|17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26|17:32:23.955] === makeBLSProofOfPossessionFromSigner === 
INFO [08-26|17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1
```

## authorizeValidatorSignerBySignature

Call this method before becoming a validator.

If you need to authorize another account to perform on-chain consensus operations instead of using the validator
account, you can call this method to grant authorization. This allows another account to perform on-chain consensus
operations.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path of the keystore file.
- `signer`: The address of the signer account.
- `signature`: The ECDSA signature generated using the [makeECDSASignatureFromSigner](#makeECDSASignatureFromSigner)
  command.
-

```shell                                                                                               
./marker authorizeValidatorSignerBySignature 
--rpcaddr http://127.0.0.1:7445 
--keystore ./account.json 
--signer 0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8 
--signature 0x59dff185...32f0d700

RESPONSE:
INFO [07-08|14:55:00.015] authorizeValidatorSignerBySignature      signer=0x26654eb0bb935dce4a34daa3e14c67662a8aa1f8    signature=0x59dff185...32f0d700
INFO [07-08|14:55:00.032] Please waiting                           func=getResult                 txHash =0xb73a1376e661d523e44b87c37e2e03cc36534d3a550808245f263aaad358b0ad
INFO [07-08|14:55:05.078] Transaction Success                      func=queryTx                  block Number=16

```

## registerByProof

Register a validator using the proof generated by `generateSignerProof`.

Parameter description:

- `rpcaddr`: The address of the RPC service, which can be either the
  provided [RPC service address](../../network/relay-chain.md) or your own RPC service
  address.
- `keystore`: The path of the keystore file.
- `proof`: The proof generated using the [generateSignerProof](#generateSignerProof) command.
- `commission`: The commission rate for the validator, expressed as a ratio relative to 1000000. The range of the
  commission parameter is from 0 to 1000000. If you want to set the commission rate to 15%, you would set this parameter
  to 150000 (150000/1000000=15%). This is one of the parameters that voters consider when voting.

```shell
./marker registerByProof 
--rpcaddr http://127.0.0.1:7445 
--keystore ./account.json -
--proof 0xf90149b8...0e56f0ab1 
--commission 150000

RESPONSE:
INFO [08-26|17:32:25.055] registerValidatorByProof                 commission=150,000
INFO [08-26|17:32:25.548] === getTotalVotesForValidator ===        admin=0x73bc690093b9dd0400c91886184a60cc127b2c33
INFO [08-26|17:32:25.974] === getTotalVotesForValidator ===        result=0
INFO [08-26|17:32:26.011] TxInfo                                   func=sendContractTransaction TX data nonce =7  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =1,098,789
INFO [08-26|17:32:26.119] Please waiting                           func=getResult                txHash =0xfb0487e7196df7489e90ab91217251da5fc7d07b2bc2d2f4ea67966a506a4cd6
INFO [08-26|17:32:27.241] Transaction Success                      func=queryTx 
```