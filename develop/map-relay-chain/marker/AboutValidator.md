## 關於驗證者

驗證器的註冊註銷等介紹。

### 註冊

註冊一個新的驗證者。 通過這條指令，我們會將您的Commission、您的ecdsaPublicKey、您的blsPublicKey、您的blsG1PublicKey、您的BLSProof轉入管理合約。 管理和保護您的資產。 你的 ecdsaPublicKey、你的 blsPublicKey、你的 BLSProof 我們會通過你的密鑰庫得到它們。
驗證者用於達成共識的 ECDSA 公鑰應與驗證者簽名者匹配。 64 字節。
驗證者用於達成共識的 BLS 公鑰應該通過所有權證明。 129 字節。
驗證器用於達成共識的 BLS G1 公鑰。 129 字節。
BLS 公鑰所有權證明，由帳戶地址上的簽名組成。 129 字節。

```shell
USAGE
  $ ./marker register

OPTIONS
  --keystore                                                   your keystore file path

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
./marker register
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
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
  $ ./marker quicklyRegister

OPTIONS
  --keystore                                                   your keystore file path
  
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

###註銷

註銷一個驗證者。

當然，首先你得是驗證者。

`Validators` 合約設置了成為驗證者的最短時間（默認 60 天）。 您必須大於這個時間才能註銷驗證器。

為了防止註銷時惡意佔用資源，我們將您的註銷請求置為pending狀態，並在epoch的最後一個區塊進行批量註銷。

```shell
USAGE
  $ ./marker deregister

OPTIONS
  --keystore                                                   your keystore file path

  --rpcaddr                                                    HTTP-RPC server address 

                                                                                                            
EXAMPLES:
./marker deregister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15


RESPONSE:
success
or
Failed
```

### 恢復註冊

如果您在當前時期註銷您的帳戶，您可以在同一時期恢復您的驗證者身份。
```shell
USAGE
  $ ./marker revertRegister

OPTIONS
  --keystore                                                   your keystore file path

  --rpcaddr                                                    HTTP-RPC server address 

                                                                                                            
EXAMPLES:
./marker revertRegister
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15


RESPONSE:
success
or
Failed
```

### authorizeValidatorSigner

- 在成為驗證者之前調用此方法
- 如果需要授權一個賬戶代替驗證者完成鏈上共識操作，請調用該方法進行授權
```shell
USAGE
  $ ./marker authorizeValidatorSigner

OPTIONS
  --keystore                                                   your keystore file path

  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
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

### MakeECDSASignatureFromSigner

- 注意：簽名者功能
- 打印 ECDSASignature 簽名者簽署賬戶（驗證者）
```shell
USAGE
  $ ./marker makeECDSASignatureFromSigner

OPTIONS
                                                             
  --target                                                     the target you will sign the message 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
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


### MakeBLSProofOfPossessionFromSigner

- 注意：簽名者功能
- 打印一個 BLSProofOfPossession 簽名者 BLSSign 帳戶（驗證者）
```shell
USAGE
  $ ./marker makeBLSProofOfPossessionFromSigner

OPTIONS
                                                             
  --target                                                     the target you will sign the message 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
makeBLSProofOfPossessionFromSigner
--signerPriv
c59c8a05f70b5ea0a0f2a2bd9491686a8c4a55c0585db2c8c6ed7ccfa0ee2c7b
--target
0xac146d6629F8C3B8F2e830275B583C5402032472

RESPONSE:
INFO [05-30|13:52:40.371] === makeBLSProofOfPossessionFromSigner === 
INFO [05-30|13:52:40.375] === pop ===                                result=0x1417ef1814518ade2af0e52ce7a11bcf834bba5ac42f91f3a1229c072721bb1b0c82513600690ebc0244572dd459d280abd6c14c0fc4837fa06335c88457a402

```




### signerToAccount
- 查詢目標籤名者的authorizingAccount
```shell
USAGE
  $ ./marker signerToAccount

                                                             
OPTIONS
  --keystore                                                   your keystore file path

  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --target                                                     target signer address
                                                                                                            
EXAMPLES:
signerToAccount
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2022-05-11T03-31-08.562982000Z--6621f2b6da2bed64b5ffbd6c5b2138547f44c8f9
--target "0x05D0CFd882185dEB9b3E0eA7872Ad332acB9E31d"

RESPONSE:
INFO [05-30|13:52:40.371] === signerToAccount === 
INFO [05-30|13:56:05.126] signerToAccount                          authorizingAccount=0xa88cF1842AC49A22435D6B4e4c1d8782e3C29EfE

```

### 生成簽名證明

生成簽名者證明

```shell
USAGE
  $ ./marker generateSignerProof

OPTIONS
                                                             
  --validator                                                  The target you will sign the message 
                                                               
  --signerPriv                                                 The private key of signaddress is used to generate sign data    
                                                                                                            
EXAMPLES:
./marker generateSignerProof 
--validator 0x73bc690093b9dd0400c91886184a60cc127b2c33 
--signerPriv 040939e5...604b6f25

RESPONSE:
INFO [08-26|17:32:23.950] generateBLSProof                         validator=0x01ccDcd1aE63a2C6B4c3493983dc6400C63729Ad signerPrivate=040939e5...604b6f25
INFO [08-26|17:32:23.955] === makeBLSProofOfPossessionFromSigner === 
INFO [08-26|17:32:23.958] generateBLSProof                         proof=0xf90149b8...0e56f0ab1

```

### authorizeValidatorSignerBySignature

- 在成為驗證者之前調用此方法
- 如果需要授權一個賬戶代替驗證者完成鏈上共識操作，請調用
   這種授權方法

```shell
USAGE
  $ ./marker authorizeValidatorSignerBySignature

OPTIONS
                                                             
  --rpcaddr                                                    HTTP-RPC server address
  
  --keystore                                                   Your keystore file path
  
  --signer                                                     Address of signer
  
  --signature                                                  ECDSA signature from signer
                                                                                                            
EXAMPLES:
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

### 註冊證明

使用 [generateSignerProof](#generatesignerproof) 生成的證明註冊驗證器

```shell
USAGE
  $ ./marker registerByProof

OPTIONS

  --rpcaddr                                                    HTTP-RPC server address
  
  --keystore                                                   Your keystore file path
                                                                                                                         
  --proof                                                      The proof generated by generateSignerProof
                                                                                                                         
  --commission                                                 The proportion of awards collected by the validator,
                                                               and then the rest to voter, The commission parameter 
                                                               is relative to 1000000 (0 < commission <1000000)
                                                               can`t be greater than 1000000. This attribute is one of 
                                                               the objects that voters refer to when voting.                                                                                                                       

EXAMPLES:
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
