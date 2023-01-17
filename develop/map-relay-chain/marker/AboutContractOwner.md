### Contract Owner 

Owner is a specific person who can modify contract rule settings.

This module will introduce how the owner modifies the contract params on the chain through  commands.

### setValidatorLockedGoldRequirements

Updates the Locked Gold requirements for Validators.

```shell
USAGE
  $ ./marker setValidatorLockedGoldRequirements

OPTIONS
  --keystore                                                   Keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 

  --value                                                      The threshold you set to 
                                                               be the validator value.         

  --duration                                                   Minimum time (in seconds)
                                                               required to be a validator,
                                                               so you will not be able to
                                                               deregister during this period.
                                                               
EXAMPLES:
./marker      setValidatorLockedGoldRequirements
--rpcaddr     http://127.0.0.1:7445
--keystore    ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--value       0
--duration    0

RESPONSE:
success
or
Failed
```


### setEpochRelayerPaymentFraction

Sets the relayer reward percentage

```shell
USAGE
  $ ./marker setEpochRelayerPaymentFraction

OPTIONS
  --keystore                                                   Keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 

  --relayerf                                                   The percentage of the total
                                                               reward to be sent to the relayer.
                                                               (0<value<1)         

  
EXAMPLES:
./marker setEpochRelayerPaymentFraction
--rpcaddr http://127.0.0.1:7445
--keystore ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--relayerf 0.1


RESPONSE:
success
or
Failed
```


### setImplementation

Sets the address of the implementation contract and calls into it.

```shell
USAGE
  $ ./marker setImplementation

OPTIONS
  --keystore                                                   Keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --contractAddress                                            Proxy contract Address    
  
  --ImplementationAddress                                      the target contractAddress 
                                                               you will set   
                                                                    
   
  
EXAMPLES:
./marker                setImplementation
--rpcaddr               http://127.0.0.1:7445
--keystore              ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--contractAddress       0x000000000000000000000000000000000000D012
--ImplementationAddress 0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5


RESPONSE:
success
or
Failed
```


### setContractOwner

Transfers ownership of the contract to a new account (`newOwner`).

```shell
USAGE
  $ ./marker setContractOwner

OPTIONS
  --keystore                                                   Keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --contractAddress                                            Proxy contract Address    
  
  --target                                                     new owner you will set   
                                                                    
   
  
EXAMPLES:
./marker                setContractOwner
--rpcaddr               http://127.0.0.1:7445
--keystore              ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--contractAddress       0x000000000000000000000000000000000000D012
--target                0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5


RESPONSE:
success
or
Failed
```


### setProxyContractOwner

Transfers ownership of Proxy to a new owner.

```shell
USAGE
  $ ./marker setProxyContractOwner

OPTIONS
  --keystore                                                   Keystore file path
  
  --rpcaddr                                                    HTTP-RPC server address 
                                                               
  --contractAddress                                            Proxy contract Address    
  
  --target                                                     new owner you will set     
                                                                    
   
  
EXAMPLES:
./marker                setProxyContractOwner
--rpcaddr               http://127.0.0.1:7445
--keystore              ./UTC--2021-09-08T08-00-15.473724074Z--1c0edab88dbb72b119039c4d14b1663525b3ac15
--contractAddress       0x000000000000000000000000000000000000D012
--target                0x6C5938B49bACDe73a8Db7C3A7DA208846898BFf5


RESPONSE:
success
or
Failed
```

