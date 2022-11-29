# How to use MCS

## Build and Deploy

### Build and deploy on EVM chains

### Build and deploy on Near Protocol


## Register chain


## Register token


1. Relay Chain deploy vault token
   Every token has a vault token. The vault token will distribute to the users that provide cross-chain liquidity.
   The mos relay contract is manager of all vault tokens.

````
npx hardhat vaultDeploy --token <relaychain token address> --name <vault token name> --symbol <vault token symbol> --network <network>

npx hardhat vaultAddManager --vault <vault token address> --manager <manager address> --network <network>
````

2. Register token
````
npx hardhat relayRegisterToken --token <relaychain mapping token address> --vault <vault token address> --mintable <true/false> --network <network>
````

3. Set fee ratio to relay chain
```
npx hardhat relaySetTokenFee --token <token address> --chain <relay chain id>  --min <minimum fee value> --max <maximum fee value> --rate <fee rate 0-1000000> --network <network>
```

## Add Cross-chain Token

1. Relay Chain Bind the token mapping relationship between the two chains that requires cross-chain
````
npx hardhat relayMapToken --token <relay chain token address> --chain <cross-chain id> --chaintoken <cross-chain token> --decimals <cross-chain token decimals> --network <network>
````

2. Relay Chain sets the token cross-chain fee ratio
````
npx hardhat relaySetTokenFee --token <token address> --chain <chain id>  --min <minimum fee value> --max <maximum fee value> --rate <fee rate 0-1000000> --network <network>
````

3. Altchain sets token mintable

````
npx hardhat mosSetMintableToken --token <token address> --mintable <true/false> --network <network>
````

**NOTE:** If set the token mintable, the token must grant the minter role to mos contract.

4. Altchain sets bridge token

````
npx hardhat mosRegisterToken --token <token address> --chains < chain ids,separated by ',' > --network <network>
````



## Token cross-chain transfer deposit

1. token transfer
```
npx hardhat transferOutToken --mos <mos or relay address> --token <token address> --address <receiver address> --value <transfer value> --chain <chain id> --network <network>
```

2. token depsit
```
npx hardhat depositOutToken --mos <mos address> --token <token address> --address <receiver address> --value <transfer value> --network <network>
```

Note that the --token parameter is optional, if not set, it means to transfer out Native Token.
Similarly --address is also an optional parameter. If it is not filled in, it will be the default caller's address.

transfer native token to other chain:
```
npx hardhat depositOutToken --mos <mos or relay address>  --address <receiver address> --value <transfer value> --network <network>
```

transfer native token to sender's address:
```
npx hardhat transferOutToken --mos <mos or relay address> --value <transfer value> --chain <chain id> --network <network>
```


## List token mapped chain

1. relay chain
```
npx hardhat relayList --relay <relay address> --token <token address> --network <network>
```

2. altchains
```
npx hardhat mosList --mos <relay address> --token <token address> --network <network>
```