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

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port

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
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
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

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port

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
                                                                                                            
EXAMPLES:
./Marker quicklyRegister
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--commission 0.1
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

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port

                                                                                                            
EXAMPLES:
./Marker deregister
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
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

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port

                                                                                                            
EXAMPLES:
./Marker revertRegister
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""


RESPONSE:
success
or
Failed
```

