# 如何在 EVM 鏈上使用 MOS

## 創建

```shell
git clone https://github.com/mapprotocol/map-contracts.git
cd map-contracts/mcs/evmv2/
npm install
```

## 測試

```shell
npx hardhat test
```


## 部署

### MOS中繼
以下步驟有助於在 Map 主網或 Makalu 測試網上部署 MOS 中繼合約

1.部署費用中心和代幣註冊
```
npx hardhat deploy --tags TokenRegister --network <network>
````
2. 部署 MOS 中繼
```
npx hardhat relayDeploy --wrapped <wrapped token> --lightnode <lightNodeManager address> --network <network>
````

* `wrapped token` 是 MAP 主網或 MAP Makalu 上的包裝 MAP 代幣地址。
* `lightNodeManager 地址` 是部署在 MAP 主網或 MAP Makalu 上的輕客戶端管理器地址。 有關詳細信息，請參閱 [此處](../protocol/README.md)。

3. 初始化MOS 中繼
```
npx hardhat relayInit  --tokenmanager <token register address> --network <network>
````


4. 設置費用分配
````
npx hardhat managerSetDistributeRate --type <0 to the token vault, 1 to specified receiver> --address <fee receiver address> --rate <rate 0-1000000, uni 0.000001> --network <network>
````

### EVM 鏈上的 MOS

1. 部署
```
npx hardhat mosDeploy --wrapped <native wrapped address> --lightnode <lightnode address> --network <network>
```

2. 設置 MOS 中繼地址
   The following command on the EVM compatible chain
```
npx hardhat mosSetRelay --relay <Relay address> --chain <map chainId> --network <network>
```

3. 註冊
 以下命令適用於Map主網和Makalu測試網的跨鏈合約配置
```
npx hardhat relayRegisterChain --address <MAPOmnichainService address> --chain <chain id> --network <network>
```

## 升級

通過以下命令升級mos合約時。

請在EVM兼容鏈上執行以下命令

```
npx hardhat deploy --tags MAPOmnichainServiceV2Up --network <network>
```

請在中繼鍊主網或Makalu測試網上執行以下命令
```
npx hardhat deploy --tags MAPOmnichainServiceRelayV2Up --network <network>
```