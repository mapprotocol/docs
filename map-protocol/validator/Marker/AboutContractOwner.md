### Contract Owner 

Owner is a specific person who can modify contract rule settings.

This module will introduce how the owner modifies the contract params on the chain through  commands.

### setValidatorLockedGoldRequirements

Updates the Locked Gold requirements for Validators.

```shell
USAGE
  $ Marker setValidatorLockedGoldRequirements

OPTIONS
  --keystore                                                   Keystore file path
  
  --password                                                   Keystore file`s password 
                                                               (defult value "") 

  --rpcaddr                                                    HTTP-RPC server listening 
                                                               interface
                                                             
  --rpcport                                                    HTTP-RPC server listening 
                                                               port

  --value                                                      The threshold you set to 
                                                               be the validator value.         

  --duration                                                   Minimum time (in seconds)
                                                               required to be a validator,
                                                               so you will not be able to
                                                               deregister during this period.
EXAMPLES:
Marker setValidatorLockedGoldRequirements
--rpcaddr localhost
--rpcport 8545
--keystore ./root/data_ibft1/keystore/UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--password ""
--value 0
--duration 0

RESPONSE:
success
or
Failed
```
