# 如何在 near 協議上使用 MOS？

## 1.部署MOS合約

您可以使用以下命令部署 MOS 合約。

```shell
# configure some parameters first
MASTER_ACCOUNT=      # the master account to create MCS account
MCS_ACCOUNT=         # MCS contract account to create
CLIENT_ACCOUNT=      # MAP light client contract account
MAP_BRIDGE_ADDRESS=  # MCS contract address on MAP blockchain
WNEAR_ACCOUNT=       # "wrap.testnet" for testnet, "wrap.near" for mainnet 
NEAR_CHAIN_ID=       # Near blockchain ID
MCS_WASM_FILE=       # path to the MCS contract wasm file
INIT_ARGS_MCS='{
              "map_light_client": "'$CLIENT_ACCOUNT'",
              "map_bridge_address": "'$MAPO_BRIDGE_ADDRESS'",
              "wrapped_token": "'$WNEAR_ACCOUNT'",
              "near_chain_id": '$NEAR_CHAIN_ID'
            }'
            
echo "creating MCS contract account"
near create-account $MCS_ACCOUNT --masterAccount $MASTER_ACCOUNT --initialBalance 40

echo "deploying mcs contract"
near deploy --accountId $MCS_ACCOUNT --wasmFile $MCS_WASM_FILE

echo "initializing mcs contract"
near call $MCS_ACCOUNT init "$INIT_ARGS_MCS" --accountId $MASTER_ACCOUNT --gas 80000000000000
```

## 2.管理MCS代幣和轉出MCS代幣

### 2.1部署MCS令牌並設置元數據

```shell

MCS_TOKEN_NAME="usdt"  # the MCS token you want to deploy
MCS_TOKEN=$MCS_TOKEN_NAME.$MCS_ACCOUNT  # the MCS token contract address
DECIMALS=24            # the MCS token decimal

echo "deploying $MCS_TOKEN_NAME contract"
near call $MCS_ACCOUNT deploy_mcs_token '{"address": "'$MCS_TOKEN_NAME'"}'  --accountId $MASTER_ACCOUNT --deposit 10 --gas 80000000000000

echo "set decimal metadata of token $MCS_TOKEN to $DECIMALS"
near call $MCS_ACCOUNT set_metadata '{"address": "'$$MCS_TOKEN'", "decimals": '$DECIMALS'}'  --accountId $MCS_ACCOUNT --gas 80000000000000
```

### 2.2 add target chain for the MCS token to transfer to
```shell
TO_CHAIN=34434  # target chain ID

echo "adding $MCS_TOKEN to_chain $TO_CHAIN to mcs contract"
near call $MCS_ACCOUNT add_mcs_token_to_chain '{"token": "'$MCS_TOKEN'", "to_chain": '$TO_CHAIN'}' --accountId $MCS_ACCOUNT

echo "getting mcs token list from mcs contract"
near view $MCS_ACCOUNT get_mcs_tokens '{}'
```

### 2.3 轉出一定數量（如果有人已經轉入一定數量的MCS代幣）

您可以看到 MCS 代幣轉出前後的金額變化。
```shell

FROM=                                                                          # sender address on NEAR blockchain
TO="[46,120,72,116,221,179,44,215,151,93,104,86,91,80,148,18,165,181,25,244]"  # the receiver address in bytes on target blockchain
AMOUNT=150000000000000000000000

echo "before transfer out, get account $FROM balance of token $MCS_TOKEN"
near view $MCS_TOKEN ft_balance_of '{"account_id":"'$FROM'"}'

echo "transfer out $AMOUNT $MCS_TOKEN token from $FROM to $TO on chain $TO_CHAIN"
near call $MCS_ACCOUNT transfer_out_token '{"token":"'$MCS_TOKEN'", "to":'$TO', "amount":"'$AMOUNT'", "to_chain":'$TO_CHAIN'}' --accountId $FROM --gas 60000000000000

echo "after transfer out, get account $FROM balance of token $MCS_TOKEN"
near view $MCS_TOKEN ft_balance_of '{"account_id":"'$FROM'"}'
```

### 2.4 刪除目標鏈以禁用傳輸到
```shell
echo "removing $MCS_TOKEN to_chain $TO_CHAIN from mcs contract"
near call $MCS_ACCOUNT remove_mcs_token_to_chain '{"token": "'$MCS_TOKEN'", "to_chain": '$TO_CHAIN'}' --accountId $MCS_ACCOUNT
```

## 3.管理可置換代幣並轉出可置換代幣
MOS token 和 可置換代幣 的區別在於，MOS 合約擁有鑄造/銷毀 MOS token 的權限，而可置換代幣 是其他人部署的代幣，已經存在於 NEAR 區塊鏈上。

### 3.1 為可替代令牌添加目標鏈以轉移到
```shell
TO_CHAIN=34434            # target chain ID
FT_TOKEN="wrap.testnet"   # fungible token contract address

echo "adding $FT_TOKEN to_chain $TO_CHAIN to mcs contract"
near call $MCS_ACCOUNT add_fungible_token_to_chain '{"token": "'$FT_TOKEN'", "to_chain": '$TO_CHAIN'}' --accountId $MCS_ACCOUNT --gas 150000000000000

echo "getting fungible token list from mcs contract"
near view $MCS_ACCOUNT get_fungible_tokens '{}'
```

### 3.2 轉出一些金額

您可以看到可替代代幣轉移前後的金額變化。
```shell

FROM=                                                                          # sender address on NEAR blockchain
TO="[46,120,72,116,221,179,44,215,151,93,104,86,91,80,148,18,165,181,25,244]"  # the receiver address in bytes on target blockchain

echo "before transfer out, get account $FROM balance of token $FT_TOKEN"
near view $FT_TOKEN ft_balance_of '{"account_id":"'$FROM'"}'

echo "transfer out $AMOUNT $FT_TOKEN token from $FROM to $TO on chain $TO_CHAIN"
near call $FT_TOKEN ft_transfer_call '{"receiver_id":"'$MCS_ACCOUNT'", "amount":"'$AMOUNT'", "memo": "", "msg": "{\"typ\": 0, \"to\": '$TO', \"to_chain\": '$TO_CHAIN'}"}' --accountId $FROM --depositYocto 1 --gas 60000000000000

echo "after transfer out, get account $FROM balance of token $FT_TOKEN"
near view $FT_TOKEN ft_balance_of '{"account_id":"'$FROM'"}'
```
### 3.3 刪除目標鏈以禁用傳輸到
```shell
echo "removing $FT_TOKEN to_chain $TO_CHAIN from mcs contract"
near call $MCS_ACCOUNT remove_fungible_token_to_chain '{"token": "'$FT_TOKEN'", "to_chain": '$TO_CHAIN'}' --accountId $MCS_ACCOUNT
```
## 4. 管理原生代幣（NEAR）和轉出原生代幣

### 4.1 為原生代幣轉入添加目標鏈
```shell
TO_CHAIN=34434            # target chain ID

echo "adding native token to_chain $TO_CHAIN to mcs contract"
near call $MCS_ACCOUNT add_native_to_chain '{"to_chain": '$TO_CHAIN'}' --accountId $MCS_ACCOUNT --gas 150000000000000

echo "getting native token list from mcs contract"
near view $MCS_ACCOUNT get_native_token_to_chains '{}'
```

### 4.2 轉出一些金額

可以看到原生代幣轉出前後的金額變化。
```shell

FROM=                                                                          # sender address on NEAR blockchain
TO="[46,120,72,116,221,179,44,215,151,93,104,86,91,80,148,18,165,181,25,244]"  # the receiver address in bytes on target blockchain

echo "before transfer out, get state of account $FROM"
near state $FROM

echo "transfer out $AMOUNT Yocto NEAR from $FROM to $TO on chain $TO_CHAIN"
near call $MCS_ACCOUNT transfer_out_native '{ "to":'$TO', "to_chain": '$TO_CHAIN'}' --accountId $FROM --depositYocto $AMOUNT --gas 100000000000000

echo "after transfer out, get state of account $FROM"
near state $FROM
```

### 4.3 刪除目標鏈以禁用傳輸到
```shell
echo "removing native token to_chain $TO_CHAIN from mcs contract"
near call $MCS_ACCOUNT remove_native_to_chain '{ "to_chain": '$TO_CHAIN'}' --accountId $MCS_ACCOUNT
```
