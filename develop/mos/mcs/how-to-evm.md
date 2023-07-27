# How to use MCS on EVM chains

## Build

```shell
git clone https://github.com/mapprotocol/map-contracts.git
cd map-contracts/mcs/evmv2/
npm install
```

## Test

```shell
npx hardhat test
```


## Deploy

### MOS Relay
The following steps help to deploy MOS relay contracts on Map mainnet or Makalu testnet

1. Deploy Fee Center and Token Register
```
npx hardhat deploy --tags TokenRegister --network <network>
````
2. Deploy MOS Relay

```
npx hardhat relayDeploy --wrapped <wrapped token> --lightnode <lightNodeManager address> --network <network>
````

* `wrapped token` is wrapped MAP token address on MAP mainnet or MAP Makalu.
* `lightNodeManager address` is the light client mananger address deployed on MAP mainnet or MAP Makalu. See [here](../protocol/README.md) for more information.

3. Init MOS Relay
```
npx hardhat relayInit  --tokenmanager <token register address> --network <network>
````


4.  sets fee distribution
````
npx hardhat managerSetDistributeRate --type <0 to the token vault, 1 to specified receiver> --address <fee receiver address> --rate <rate 0-1000000, uni 0.000001> --network <network>
````

### MOS on EVM Chains

1. Deploy
```
npx hardhat mosDeploy --wrapped <native wrapped address> --lightnode <lightnode address> --network <network>
```

2. Set MOS Relay Address
   The following command on the EVM compatible chain
```
npx hardhat mosSetRelay --relay <Relay address> --chain <map chainId> --network <network>
```

3. Register
   The following command applies to the cross-chain contract configuration of Map mainnet and Makalu testnet
```
npx hardhat relayRegisterChain --address <MAPOmnichainService address> --chain <chain id> --network <network>
```


## Upgrade

When upgrade the mos contract through the following commands.

Please execute the following command on the EVM compatible chain

```
npx hardhat deploy --tags MAPOmnichainServiceV2Up --network <network>
```

Please execute the following command on relay chain mainnet or Makalu testnet
```
npx hardhat deploy --tags MAPOmnichainServiceRelayV2Up --network <network>
```