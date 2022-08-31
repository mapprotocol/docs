## AboutValidator

Introduction register deregister and so on about validator.

### Register

Register a new Validator. Through this command, we will transfer your Commission、your ecdsaPublicKey、your blsPublicKey、your blsG1PublicKey、your BLSProof into the management contract. To manage and secure your assets. your ecdsaPublicKey、your blsPublicKey、your BLSProof we'll get them through your keystore. 
The ECDSA public key that the validator is using for consensus, should match the validator signer. 64 bytes. 
The BLS public key that the validator is using for consensus, should pass proof of possession. 129 bytes. 
The BLS G1 public key that the validator is using for consensus. 129 bytes. 
The BLS public key proof-of-possession, which consists of a signature on the account address. 129 bytes.

```shell
USAGE
  $ ./Marker register

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server address 

  --commission                                                 The proportion of awards 
                                                               collected by the validator,
                                                               and then the rest to voter
                                                               ,The commission parameter 
                                                               is relative to 1000000
                                                               (0 < commission <1000000)
                                                               can`t be greater than 1000000.
                                                               This attribute is one of the 
                                                               objects that voters refer to
                                                               when voting.                                                                                                                            

  
EXAMPLES:
./Marker register
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--commission 0.1

RESPONSE:
success
or
Failed
```

### quicklyRegister

If you have not creat Account or locked the Map, you can quickly register through the `quicklyRegister` command, which
integrates the `createAccount` `lockedMAP`.

Please note that you can only use this command once. No matter whether the command succeeds or fails, this command only packs and combines `createAccount` `lockedMAP` commands and does not have the nature of reuse.


```shell
USAGE
  $ ./Marker quicklyRegister

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server address 

  --commission                                                 The proportion of awards 
                                                               collected by the validator,
                                                               and then the rest to voter
                                                               ,Commission(0< commission <1000000)
                                                               can`t be greater than 1000000.
                                                               This attribute is one of the 
                                                               objects that voters refer to
                                                               when voting 
                                                               
  --lockedNum                                                  In order to register as a 
                                                               validator,you will lock the 
                                                               `MAP`. 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data                                            
                                                                                                            
EXAMPLES:
./Marker quicklyRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--commission 100000
--signerPriv 842e1d8a93b4e46104da96676066ddb0973c63ec80a15746856c046dd4a1004c
--lockedNum 1000000

RESPONSE:
success
or
Failed
```

### deregister

deregister a validator.

Of course, first you have to be a validator.

The `Validators` contract sets the minimum time(default 60 Day) to become a validator. You must be greater than this time before you can deregister validator.

In order to prevent malicious occupation of resources during deregister, we put your deregister request in pending status and perform batch logout in the last block of the epoch.

```shell
USAGE
  $ ./Marker deregister

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server address 

                                                                                                            
EXAMPLES:
./Marker deregister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""


RESPONSE:
success
or
Failed
```

### revertRegister

if you deRegister your account in the current epoch you can revert your validator identity at the same epoch.
```shell
USAGE
  $ ./Marker revertRegister

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server address 

                                                                                                            
EXAMPLES:
./Marker revertRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""


RESPONSE:
success
or
Failed
```

### authorizeValidatorSigner

- call this method before become to be a validator
- If you need to authorize an account to complete the on chain consensus operation instead of the validator, please call this method for authorization
```shell
USAGE
  $ ./Marker authorizeValidatorSigner

OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
./Marker authorizeValidatorSigner
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--signerPriv   842e1d8a93b4e46104da96676066ddb0973c63ec80a15746856c046dd4a1004c   

RESPONSE:
INFO [05-30|13:41:55.131] === authorizeValidatorSigner === 
INFO [05-30|13:41:55.140] TxInfo                                   func=sendContractTransaction  TX data nonce =3  gasLimit =4,500,000  gasPrice =101,000,000,000  chainID =214
INFO [05-30|13:41:55.141] Please waiting                           func=getResult                 txHash =0x6ccc9758199c105c2b8eb9f9807b553baaa9062ec871a1fc47f3866a4a1e8d49
INFO [05-30|13:42:03.231] Transaction Success                      func=queryTx                  block Number=37

```

### MakeECDSASignatureFromSigner

- note:signer function 
- print a ECDSASignature that signer sign the account(validator)
```shell
USAGE
  $ ./Marker makeECDSASignatureFromSigner

OPTIONS
                                                             
  --target                                                     the target you will sign the message 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
makeECDSASignatureFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--validator
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:41:55.131] === ECDSASignature === 
INFO [05-30|13:41:55.131] === signer ===                           account=0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d
INFO [05-30|13:41:55.131] ECDSASignature                           result=0x6dcec34a67a3388b6fbf93ad48f88aa88c0ce46789de0f9e042acbe6e116c97f26d645652576a65602ed97f7ca16f890898b8e761de3edc34447edb2e41713ad01


```


### MakeBLSProofOfPossessionFromSigner

- note:signer function
- print a BLSProofOfPossession that signer BLSSign the account(validator)
```shell
USAGE
  $ ./Marker makeBLSProofOfPossessionFromSigner

OPTIONS
                                                             
  --target                                                     the target you will sign the message 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
makeBLSProofOfPossessionFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--validator
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:52:40.371] === makeBLSProofOfPossessionFromSigner === 
INFO [05-30|13:52:40.375] === pop ===                                result=0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402

```




### signerToAccount
- Query the authorizingAccount of a target signer
```shell
USAGE
  $ ./Marker signerToAccount

                                                             
OPTIONS
  --keystore                                                   your keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     target signer address
                                                                                                            
EXAMPLES:
signerToAccount
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2022-05-11T03-31-08.562982000Z--6621f2b6da2bed64b5ffbd6c5b2138547f44c8f9
--password "111111"
--target "0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d"

RESPONSE:
INFO [05-30|13:52:40.371] === signerToAccount === 
INFO [05-30|13:56:05.126] signerToAccount                          authorizingAccount=0xa88cF1842AC49A22435D6B4e4c1d8782e3C29EfE

```