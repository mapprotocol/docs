# 如何使用 MCS

## 構建和部署

[在 EVM 鏈上構建和部署](/develop/mos/mcs/how-to-evm.md)

[在 Near 協議上構建和部署](/develop/mos/mcs/how-to-evm.md)


## 註冊鏈

###註冊EVM鏈
1. 在 EVM 鏈上設置中繼合約

   ```shell
   npx hardhat mosSetRelay --relay <Relay address> --chain <map chainId> --network <network>
   ```

2. 註冊中繼合約

   ```shell
   npx hardhat relayRegisterChain --address <MOS address> --chain <chain id> --network <network>
   ```
   
### 註冊 Near

   ```shell
   npx hardhat relayRegisterChain --address <MOS address> --chain <near chain id> --type 2 --network <network>
   ```
   **NOTE**: Near Protocol testnet chain id 5566818579631833089, mainnet chain id 5566818579631833088


## 註冊代幣
1. 在中繼鏈上部署 vault token
    每個令牌都有一個保險庫令牌。 保險庫代幣將分配給提供跨鏈流動性的用戶。
    mos 中繼合約是所有金庫代幣的管理者。
   ```
   npx hardhat vaultDeploy --token <relaychain token address> --name <vault token name> --symbol <vault token symbol> --network <network>

   npx hardhat vaultAddManager --vault <vault token address> --manager <manager address> --network <network>
   ```

2. 在中繼鏈註冊代幣

   ````
   npx hardhat relayRegisterToken --token <relaychain mapping token address> --vault <vault token address> --mintable <true/false> --network <network>
   ````

3. 在中繼鏈上設置費用比例

   ```
   npx hardhat relaySetTokenFee --token <token address> --chain <relay chain id>  --min <minimum fee value> --max <maximum fee value> --rate <fee rate 0-1000000> --network <network>
   ```

## 添加跨鏈代幣

1.中繼鏈綁定需要跨鏈的兩條鏈之間的token映射關係

   ````
   npx hardhat relayMapToken --token <relay chain token address> --chain <cross-chain id> --chaintoken <cross-chain token> --decimals <cross-chain token decimals> --network <network>
   ````

2. Relay Chain設置代幣跨鏈手續費比例

   ````
   npx hardhat relaySetTokenFee --token <token address> --chain <chain id>  --min <minimum fee value> --max <maximum fee value> --rate <fee rate 0-1000000> --network <network>
   ````

3. Altchain 設置代幣可鑄幣表

   ````
   npx hardhat mosSetMintableToken --token <token address> --mintable <true/false> --network <network>
   ````
**注意：** 如果設置代幣可鑄幣表，則代幣必須授予 mos 合約鑄幣者角色。

4. Altchain 設置橋接代幣

   ````
   npx hardhat mosRegisterToken --token <token address> --chains < chain ids,separated by ',' > --network <network>
   ````


## 代幣跨鏈轉存

1.代幣轉移
   ```shell
   npx hardhat transferOutToken --mos <mos or relay address> --token <token address> --address <receiver address> --value <transfer value> --chain <chain id> --network <network>
   ```

2. token depsit

   ```
   npx hardhat depositOutToken --mos <mos address> --token <token address> --address <receiver address> --value <transfer value> --network <network>
   ```

   ***注意*** `--token` 參數是可選的，如果不設置，則表示轉出Native Token。
同樣 --address 也是一個可選參數。 如果不填寫，則為默認來電地址。

3. 將原生代幣轉移到其他鏈：

   ```
   npx hardhat depositOutToken --mos <mos or relay address>  --address <receiver address> --value <transfer value> --network <network>
   ```

4. 將本地代幣轉移到發送人的地址：

   ```
   npx hardhat transferOutToken --mos <mos or relay address> --value <transfer value> --chain <chain id> --network <network>
   ```


## 列出代幣映射鏈

1. 中繼鏈
   ```
   npx hardhat relayList --relay <relay address> --token <token address> --network <network>
   ```

2. 其他鏈

   ```
   npx hardhat mosList --mos <relay address> --token <token address> --network <network>
   ```