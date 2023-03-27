# How to use MOS Messsage on EVM chains

## Build

```
git clone https://github.com/mapprotocol/mapo-service-contracts.git
cd /mapo-service-contracts/evm/
npm install
```

## Test

```
npx hardhat test
```

## Deploy

### MOS Relay

The following steps help to deploy MOS relay contracts on Map mainnet or Makalu testnet

1. Deploy MOS Relay

```
npx hardhat relayFactoryDeploy --wrapped <wrapped token> --lightnode <lightNodeManager address> --network <network>
```

- `wrapped token` is wrapped MAP token address on MAP mainnet or MAP Makalu.
- `lightNodeManager address` is the light client mananger address deployed on MAP mainnet or MAP Makalu. See [here](file:///D:/workSpace/MapoProtocol/docs/develop/mos/protocol/README.md) for more information.

2. Deploy FeeService

```
npx hardhat feeFactoryDeploy --network <network>
```

3.  MOS Relay set  FeeService

```
npx hardhat setFeeService  --address <feeService address> --network <network>
```

4. MOS Relay register chain

```
npx hardhat relayRegisterChain --address <mos contract address> --chain <mos chain id> --type <optional default evm value is 1> --network <network>
```



### MOS on EVM Chains

1. Deploy MOS

```
npx hardhat mosFactoryDeploy --wrapped <native wrapped address> --lightnode <lightnode address> --network <network>
```

2. Deploy FeeService

```
npx hardhat feeFactoryDeploy --network <network>
```

3. Set MOS Relay Address The following command on the EVM compatible chain

```
npx hardhat mosFactoryDeploy --relay <Relay address> --chain <map chainId> --network <network>
```

4. MOS set  FeeService

```
npx hardhat setFeeService  --address <feeService address> --network <network>
```



## Upgrade

When upgrade the mos contract through the following commands.

Please execute the following command on the EVM compatible chain

```
npx hardhat deploy --tags MapoServiceV3Up --network <network>
```

Please execute the following command on relay chain mainnet or Makalu testnet

```
npx hardhat deploy --tags MapoServiceRelayV3Up --network <network>
```