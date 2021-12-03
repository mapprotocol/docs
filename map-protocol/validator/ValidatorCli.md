## Validator CLI
### What is the Validator CLI
The Command Line Interface allows users to interact with the Atlas Protocol smart contracts.

### Building ValidatorCli

```bash
go build -o ValidatorCli  *.go
```
###  CreateAccount
```bash

create a account

USAGE
  $ ValidatorCli  createAccount --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key	file
  
  --password                                                 your atlas account password
  
  --namePrefix                                               distinguish between groups and verifiers("validator","group")
  

EXAMPLES
  ValidatorCli createAccount --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-07-11.808701800Z--3e3429f72450a39ce227026e8ddef331e9973e4d --password "123456" --namePrefix "validator"
 ```


###  LockedMAP
```bash

locked Account MAP

USAGE
  $ ValidatorCli  lockedMAP --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key	file
  
  --password                                                 your atlas account password
  

EXAMPLES
  ValidatorCli lockedMAP --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-07-11.808701800Z--3e3429f72450a39ce227026e8ddef331e9973e4d --password "123456"
  ```

###  Affiliate
```bash

affiliate validator Account to groupAddress

USAGE
  $ ValidatorCli  affiliate --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key	file
  
  --password                                                 your atlas account password
  
  --groupAddress                                             target groupAddress
  

EXAMPLES
  ValidatorCli affiliate --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-07-11.808701800Z--3e3429f72450a39ce227026e8ddef331e9973e4d --password "123456" ----groupAddress "0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5"
 ```


###  RegisterGroup
```bash

registered Validator Group

USAGE
  $ ValidatorCli  registerGroup --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key	file
  
  --password                                                 your atlas account password
  
  --commission                                               register group param,This represents the share of the epoch rewards given to elected Validators that goes to the group they are a member of

EXAMPLES
  ValidatorCli registerGroup --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-07-11.808701800Z--3e3429f72450a39ce227026e8ddef331e9973e4d --password "123456"  --commission 80
  ```

###  RegisterValidator
```bash

registered Validator Validator

USAGE
  $ ValidatorCli  registerValidator --rpcaddr localhost  --rpcport 8545 --keystore <your keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key	file
  
  --password                                                 your atlas account password

EXAMPLES
  ValidatorCli registerValidator --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456"
  ```


###  QueryGroups
```bash

Retrun all of Groups address 

USAGE
  $ ValidatorCli  registerValidator --rpcaddr localhost  --rpcport 8545 --keystore <your keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key	file
  
  --password                                                 your atlas account password

EXAMPLES
  ValidatorCli queryGroups --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf  --password "123456"
  ```

###  GetRegisteredValidatorSigners

  ```bash

Retrun all of Register address 

USAGE
  $ ValidatorCli  getRegisteredValidatorSigners --rpcaddr localhost  --rpcport 8545 --keystore <your  keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas account private key file
  
  --password                                                 your atlas account password

EXAMPLES
  ValidatorCli registerValidator --rpcaddr localhost  --rpcport 8545 --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456"
  ```

###  GetTopGroupValidators

  ```bash

Return the first topNum Validators at this group

USAGE
  $ ValidatorCli  getTopGroupValidators --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> --topNum 5

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas group account private key	file
  
  --password                                                 your atlas group account password
  
  --topNum                                                   param about top num Validators


EXAMPLES
  ValidatorCli registerValidator --rpcaddr localhost  --rpcport 8545  --topNum 5 --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456"
  ```



###  AddFirstMember

  ```bash

add the first Validator to group

USAGE
  $ ValidatorCli  addFirstMember --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas group account private key	file
  
  --password                                                 your atlas group account password
  
  --address                                                  validator address

EXAMPLES
  ValidatorCli addFirstMember --rpcaddr localhost  --rpcport 8545  --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456" --address "0x3e3429f72450a39ce227026e8ddef331e9973e4d"
  ```




###  AddToGroup

  ```bash

add validator to group

USAGE
  $ ValidatorCli  addToGroup --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas group account private key	file
  
  --password                                                 your atlas group account password
  
   --address                                                 validator address
EXAMPLES
  ValidatorCli addToGroup --rpcaddr localhost  --rpcport 8545  --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456" --address "0x3e3429f72450a39ce227026e8ddef331e9973e4d"
  ``` 



###  RemoveMember

  ```bash

remove the Validators in group

USAGE
  $ ValidatorCli  removeMember --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas group account private key	file
  
  --password                                                 your atlas group account password
  
  --readConfig                                               get validators by validatorCfg.json

EXAMPLES
  ValidatorCli removeMember --rpcaddr localhost  --rpcport 8545  --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456" --readConfig
  ``` 


###  DeregisterValidatorGroup

```bash

deregister group account

USAGE
  $ ValidatorCli  deregisterValidatorGroup --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas group account private key	file
  
  --password                                                 your atlas group account password
  

EXAMPLES
  ValidatorCli deregisterValidatorGroup --rpcaddr localhost  --rpcport 8545  --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456"
``` 

###  DeregisterValidator

```bash

deregister Validator account

USAGE
  $ ValidatorCli  deregisterValidator --rpcaddr localhost  --rpcport 8545 --keystore <your validator keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas Validator account private key	file
  
  --password                                                 your atlas Validator account password
  

EXAMPLES
  ValidatorCli deregisterValidator --rpcaddr localhost  --rpcport 8545  --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456"
``` 



###  SetMaxGroupSize

```bash

set the max group size

USAGE
  $ ValidatorCli  setMaxGroupSize --rpcaddr localhost  --rpcport 8545 --keystore <your group keystore> 

OPTIONS
  --rpcaddr localhost                                        atlas host

  --rpcport                                                  atlas rpcport
                                                             
  --keystore                                                 your atlas group account private key	file
  
  --password                                                 your atlas group account password
  
  --maxSize                                                  the max group size
 
EXAMPLES
  ValidatorCli setMaxGroupSize --rpcaddr localhost  --rpcport 8545  --keystore /root/keystore/UTC--2021-07-19T02-09-17.552426700Z--81f02fd21657df80783755874a92c996749777bf --password "123456"
``` 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  