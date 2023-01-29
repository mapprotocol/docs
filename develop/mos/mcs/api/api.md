
## MCS API Reference

List of APIs

- [queryHistory](#queryHistory)
- [queryBridgeInfo](#queryBridgeInfo)
- [queryTokenList](#queryTokenList)
- [queryChainList](#queryChainList)

### queryHistory

Uri: /api/queryHistory

Request method: post

Returns the user's transaction history on the specified chain.

##### Parameters

Parameter Type： raw (application/json)

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`address` - User address.

`chainId` - Chain id.

Example:

{
"address": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
"chainId": 1,
"pageNo": 1,
"pageSize": 10
}

##### Returns

`hash` - Transaction hash.

`amount` - Cross-chain amount.

`tokenAddress` - Token address.

`fromChainObject` - From chain details.

`toChainObject` - To chain details.

`tokenObject` - Token details.

##### Example
```js
// Request
curl --location --request POST 'http://18.139.224.21:8201/api/queryHistory' \
--header 'Content-Type: application/json' \
--data-raw '{
"chainId": 3,
    "address": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
    "pageNo": 1,
    "pageSize": 3
}'
    
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": 61,
            "chain": "ETH",
            "fromChainid": 3,
            "contract": "0x1f027B5B09924AC420002C1d09F7241c2041045A",
            "from": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
            "to": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
            "tokenAddress": "0xd100135b823661ebde67d12dca7567723834014d",
            "amount": 1520000000.000000,
            "toChainid": 22776,
            "orderId": "0xf104313352a15d1d91ed87cb5b8019fc97762d6517aca6838fe723bede2685c5",
            "hash": "0xe5529ecc0656a227885dc97c78d472132e6d1fdee00e8a7c801dd318dce60965",
            "height": 12192675,
            "confirmHeight": 12192681,
            "relayerSendHash": null,
            "relayerSendHeight": null,
            "state": 1,
            "timestamp": "2022-04-14 06:29:47",
            "createdAt": "2022-04-14 06:30:21",
            "updatedAt": "2022-04-14 06:31:39",
            "relayerChainid": null,
            "relayerHash": null,
            "relayerHeight": null,
            "relayerContract": null,
            "relayerConfirmHeight": null,
            "type": 4,
            "transferinHash": "0x0d00e6c0e72602f0d89edee15c1fd09b3c93b3a820193dc0f8ec90b01f6eccf9",
            "transferinHeight": 2158848,
            "inAmount": 1520000000.000000,
            "transferinContract": "0xd505bfdb4f7fe74d4ffa0d641eddfbe3a344a671",
            "transferinSendHash": "0x0d00e6c0e72602f0d89edee15c1fd09b3c93b3a820193dc0f8ec90b01f6eccf9",
            "transferinConfirmHeight": 2158854,
            "transferinSendHeight": null,
            "usdtValume": 1520.693726,
            "fromChainObject": "{\"chain\":\"ETH\",\"chainId\":3,\"chainImg\":\"https://get.celer.app/cbridge-icons/chain-icon/ETH.png\",\"chainName\":\"Ethereum Rospten\",\"contract\":\"0x1f027B5B09924AC420002C1d09F7241c2041045A\",\"gasLimit\":\"600000\",\"id\":2,\"rpc\":\"https://ropsten.infura.io/v3/7d01bfb33f364de08d1377e3d1a843b2\",\"scanUrl\":\"https://ropsten.etherscan.io/\"}",
            "toChainObject": "{\"chain\":\"MAP\",\"chainId\":22776,\"chainImg\":\"https://files.maplabs.io/bridge/map.png\",\"chainName\":\"MAP Makalu\",\"contract\":\"0x8084d0C99217221a8d233B3162C724F676295982\",\"gasLimit\":\"600000\",\"id\":1,\"rpc\":\"https://poc2-rpc.maplabs.io/\",\"scanUrl\":\"https://makalu.mapscan.io/\"}",
            "tokenObject": "{\"address\":\"0xD100135b823661EbdE67d12dCA7567723834014d\",\"chainId\":3,\"decimal\":6,\"id\":1,\"img\":\"https://files.maplabs.io/bridge/usdt.png\",\"isMint\":0,\"name\":\"USDT\",\"symbol\":\"USDT\",\"tokenId\":\"USDT\"}"
        },
        {
            "id": 56,
            "chain": "ETH",
            "fromChainid": 3,
            "contract": "0x1f027B5B09924AC420002C1d09F7241c2041045A",
            "from": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
            "to": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
            "tokenAddress": "0xd100135b823661ebde67d12dca7567723834014d",
            "amount": 100000000.000000,
            "toChainid": 22776,
            "orderId": "0xbd8f154ddf9bb691a4f6d3ecdfdf14e1f196f0f7ec0c061400db3c7b7eb9aa00",
            "hash": "0x0be2d39e5500c5de9a7a2b21a4a0577c115d7a06c8b554f5c9e224def6b6618e",
            "height": 12184824,
            "confirmHeight": 12184830,
            "relayerSendHash": null,
            "relayerSendHeight": null,
            "state": 1,
            "timestamp": "2022-04-11 10:39:20",
            "createdAt": "2022-04-11 10:39:35",
            "updatedAt": "2022-04-11 10:42:09",
            "relayerChainid": null,
            "relayerHash": null,
            "relayerHeight": null,
            "relayerContract": null,
            "relayerConfirmHeight": null,
            "type": 4,
            "transferinHash": "0xa048ad9ab9207564e21233a5593b6489aa16a623621f702b9a1b4130e69596ad",
            "transferinHeight": 2110014,
            "inAmount": 100000000.000000,
            "transferinContract": "0xd505bfdb4f7fe74d4ffa0d641eddfbe3a344a671",
            "transferinSendHash": "0xa048ad9ab9207564e21233a5593b6489aa16a623621f702b9a1b4130e69596ad",
            "transferinConfirmHeight": 2110020,
            "transferinSendHeight": null,
            "usdtValume": 100.045639,
            "fromChainObject": "{\"chain\":\"ETH\",\"chainId\":3,\"chainImg\":\"https://get.celer.app/cbridge-icons/chain-icon/ETH.png\",\"chainName\":\"Ethereum Rospten\",\"contract\":\"0x1f027B5B09924AC420002C1d09F7241c2041045A\",\"gasLimit\":\"600000\",\"id\":2,\"rpc\":\"https://ropsten.infura.io/v3/7d01bfb33f364de08d1377e3d1a843b2\",\"scanUrl\":\"https://ropsten.etherscan.io/\"}",
            "toChainObject": "{\"chain\":\"MAP\",\"chainId\":22776,\"chainImg\":\"https://files.maplabs.io/bridge/map.png\",\"chainName\":\"MAP Makalu\",\"contract\":\"0x8084d0C99217221a8d233B3162C724F676295982\",\"gasLimit\":\"600000\",\"id\":1,\"rpc\":\"https://poc2-rpc.maplabs.io/\",\"scanUrl\":\"https://makalu.mapscan.io/\"}",
            "tokenObject": "{\"address\":\"0xD100135b823661EbdE67d12dCA7567723834014d\",\"chainId\":3,\"decimal\":6,\"id\":1,\"img\":\"https://files.maplabs.io/bridge/usdt.png\",\"isMint\":0,\"name\":\"USDT\",\"symbol\":\"USDT\",\"tokenId\":\"USDT\"}"
        },
        {
            "id": 55,
            "chain": "ETH",
            "fromChainid": 3,
            "contract": "0x1f027B5B09924AC420002C1d09F7241c2041045A",
            "from": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
            "to": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
            "tokenAddress": "0x4fef2283a7012021e43bae13aaaebe9b638d5c10",
            "amount": 1200000000000000000000.000000,
            "toChainid": 22776,
            "orderId": "0x7b1c5ea2c9bd40e50a9296953307900f298d4e97920b8083fbb9735ed4eeba10",
            "hash": "0x443a8d48eb2aedfb4dcaefd675856f234aa68ecd6645c6cd315c6c099722a622",
            "height": 12184718,
            "confirmHeight": 12184724,
            "relayerSendHash": null,
            "relayerSendHeight": null,
            "state": 1,
            "timestamp": "2022-04-11 09:43:04",
            "createdAt": "2022-04-11 09:44:21",
            "updatedAt": "2022-04-11 09:50:24",
            "relayerChainid": null,
            "relayerHash": null,
            "relayerHeight": null,
            "relayerContract": null,
            "relayerConfirmHeight": null,
            "type": 4,
            "transferinHash": "0xfd8d2883045d47cd2e4e5dd4cc9f3b968e4509cbd7b5d2dd228baeb145b1105f",
            "transferinHeight": 2109393,
            "inAmount": 1200000000000000000000.000000,
            "transferinContract": "0xd6a9f83a761d79d2448f0eb347e2cbf7c227bd6d",
            "transferinSendHash": "0xfd8d2883045d47cd2e4e5dd4cc9f3b968e4509cbd7b5d2dd228baeb145b1105f",
            "transferinConfirmHeight": 2109399,
            "transferinSendHeight": null,
            "usdtValume": 1.348144,
            "fromChainObject": "{\"chain\":\"ETH\",\"chainId\":3,\"chainImg\":\"https://get.celer.app/cbridge-icons/chain-icon/ETH.png\",\"chainName\":\"Ethereum Rospten\",\"contract\":\"0x1f027B5B09924AC420002C1d09F7241c2041045A\",\"gasLimit\":\"600000\",\"id\":2,\"rpc\":\"https://ropsten.infura.io/v3/7d01bfb33f364de08d1377e3d1a843b2\",\"scanUrl\":\"https://ropsten.etherscan.io/\"}",
            "toChainObject": "{\"chain\":\"MAP\",\"chainId\":22776,\"chainImg\":\"https://files.maplabs.io/bridge/map.png\",\"chainName\":\"MAP Makalu\",\"contract\":\"0x8084d0C99217221a8d233B3162C724F676295982\",\"gasLimit\":\"600000\",\"id\":1,\"rpc\":\"https://poc2-rpc.maplabs.io/\",\"scanUrl\":\"https://makalu.mapscan.io/\"}",
            "tokenObject": "{\"address\":\"0x4FeF2283a7012021e43Bae13aaAEBE9B638D5c10\",\"chainId\":3,\"decimal\":18,\"id\":4,\"img\":\"https://files.maplabs.io/bridge/idv.png\",\"isMint\":1,\"name\":\"MintToken\",\"symbol\":\"MTTK\",\"tokenId\":\"MintToken\"}"
        }
    ],
        "total": 11
}
}
```

### queryBridgeInfo

Uri: /api/queryBridgeInfo

Request method: post

Returns cross-chain details.

##### Parameters

Parameter Type： raw / application/json

`txHash` - Transaction hash.

Example: 

{
"txHash": "0xcdf3dbd92f6269f4ce5d4c1f05fae5673cec24fe876295fca0d6059297d389d4"
}

##### Returns

`coin` - Token symbol.

`sending` - Send amount.

`receiving` - Receive amount.

`fromHeight` - From Chain send height.

`confirmHeight` - From Chain confirm height.

`relayerHeight` - Relayer Chain height.

`relayerConfirmHeight` - Relayer Chain confirm height.

`transferInHeight` - Receive height.

`transferInConfirmHeight` - Receive confirm height.

`fromChainObject` - From chain details.

`toChainObject` - To Chain details.

`tokenObject` - Token details.

`relayerObject` - Relayer Chain details.

##### Example
```js
// Request
curl --location --request POST 'http://18.139.224.21:8201/api/queryBridgeInfo' \
--header 'Content-Type: application/json' \
--data-raw '{
"txHash": "0x47ae702930180ccbc81bfaaf7102d1dcff28fc7ac0c37d8a60caceca20794169"
}'
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "coin": "MTTK",
        "fromDecimal": 18,
        "fromChainName": "BSC Testnet",
        "toChainName": "MAP Makalu",
        "receiveDecimal": 18,
        "from": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
        "to": "0x01eb1a70b4b3529fc0c3a8dc6f78ffb5f5d42e8c",
        "sending": 10000000000000000000.000000,
        "receiving": 10000000000000000000.000000,
        "fromChainId": 97,
        "relayerChainId": null,
        "toChainId": 22776,
        "state": 1,
        "fromHeight": 18096818,
        "fromHash": "0x47ae702930180ccbc81bfaaf7102d1dcff28fc7ac0c37d8a60caceca20794169",
        "relayerHeight": null,
        "relayerHash": null,
        "transferInHeight": 1954764,
        "transferInHash": "0x0919e0ca95c090c47ea9f5c26a6b569c3ec9c000af8444613ab59124cc5ad2e1",
        "confirmHeight": 18096824,
        "relayerConfirmHeight": null,
        "transferInConfirmHeight": 1954770,
        "type": 4,
        "fromChainObject": "{\"chain\":\"BSC\",\"chainId\":97,\"chainImg\":\"https://get.celer.app/cbridge-icons/chain-icon/BSC.png\",\"chainName\":\"BSC Testnet\",\"contract\":\"0x7A3504932E9AE869057418b333d4f25330a9355A\",\"gasLimit\":\"600000\",\"id\":3,\"rpc\":\"https://data-seed-prebsc-2-s2.binance.org:8545/\",\"scanUrl\":\"https://testnet.bscscan.com/\"}",
        "toChainObject": "{\"chain\":\"MAP\",\"chainId\":22776,\"chainImg\":\"https://files.maplabs.io/bridge/map.png\",\"chainName\":\"MAP Makalu\",\"contract\":\"0x8084d0C99217221a8d233B3162C724F676295982\",\"gasLimit\":\"600000\",\"id\":1,\"rpc\":\"https://poc2-rpc.maplabs.io/\",\"scanUrl\":\"https://makalu.mapscan.io/\"}",
        "tokenObject": "{\"address\":\"0x6130c0C5c7D7A0E95fBdc462F5fFDA8fb99ca503\",\"chainId\":97,\"decimal\":18,\"id\":6,\"img\":\"https://files.maplabs.io/bridge/idv.png\",\"isMint\":1,\"name\":\"MintToken\",\"symbol\":\"MTTK\",\"tokenId\":\"MintToken\"}",
        "relayerObject": "{\"chain\":\"MAP\",\"chainId\":22776,\"chainImg\":\"https://files.maplabs.io/bridge/map.png\",\"chainName\":\"MAP Makalu\",\"contract\":\"0x8084d0C99217221a8d233B3162C724F676295982\",\"gasLimit\":\"600000\",\"id\":1,\"rpc\":\"https://poc2-rpc.maplabs.io/\",\"scanUrl\":\"https://makalu.mapscan.io/\"}"
}
}
```

### queryTokenList

Uri: /api/queryTokenList

Request method: post

Returns a list of tokens.

##### Parameters

none

##### Returns

`list` - List of tokens:
- `tokenId`: Token id.
- `address`: Token address.
- `chainId`: Chain id.
- `isMint`: Have mint permission, 0: NO, 1: YES.
- `symbol`: Token symbol.
- `decimal`: Token decimal.
- `img`: Token icon.

##### Example
```js
// Request
curl --location --request POST 'http://18.139.224.21:8201/api/queryTokenList'
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": {
        "80001": [
            {
                "id": 16,
                "tokenId": "USDT",
                "address": "0x7B97F454324C5224DC241E9b75EEa5af66D8997A",
                "name": "USDT",
                "chainId": 80001,
                "isMint": 0,
                "symbol": "USDT",
                "decimal": 6,
                "img": "https://files.maplabs.io/bridge/usdt.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 17,
                "tokenId": "MintToken",
                "address": "0x217f99f2933f6aa9e056740a8aaf903512c8f38a",
                "name": "MintToken",
                "chainId": 80001,
                "isMint": 1,
                "symbol": "MTTK",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/idv.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 18,
                "tokenId": "MAP",
                "address": "0x659BC6aD25AEea579f3eA91086fDbc7ac0432Dc4",
                "name": "MAP",
                "chainId": 80001,
                "isMint": 0,
                "symbol": "MAP",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/map.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 19,
                "tokenId": "ETH",
                "address": "0xaDd16759942D1dc2A7a2789c642b91F92bF561D7",
                "name": "ETH",
                "chainId": 80001,
                "isMint": 0,
                "symbol": "ETH",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/eth.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            }
        ],
            "97": [
            {
                "id": 3,
                "tokenId": "USDT",
                "address": "0x981a840475eeB7171538eA9F4345EDe941135CF8",
                "name": "USDT",
                "chainId": 97,
                "isMint": 0,
                "symbol": "USDT",
                "decimal": 6,
                "img": "https://files.maplabs.io/bridge/usdt.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 6,
                "tokenId": "MintToken",
                "address": "0x6130c0C5c7D7A0E95fBdc462F5fFDA8fb99ca503",
                "name": "MintToken",
                "chainId": 97,
                "isMint": 1,
                "symbol": "MTTK",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/idv.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 9,
                "tokenId": "MAP",
                "address": "0x46f0B283B606E611f701E89e6B2fA5350933E6A1",
                "name": "MAP",
                "chainId": 97,
                "isMint": 0,
                "symbol": "MAP",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/map.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 15,
                "tokenId": "ETH",
                "address": "0x33E1D55883dB6B115769A294E4E5EE5edCBdbcd1",
                "name": "ETH",
                "chainId": 97,
                "isMint": 0,
                "symbol": "ETH",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/eth.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            }
        ],
            "3": [
            {
                "id": 1,
                "tokenId": "USDT",
                "address": "0xD100135b823661EbdE67d12dCA7567723834014d",
                "name": "USDT",
                "chainId": 3,
                "isMint": 0,
                "symbol": "USDT",
                "decimal": 6,
                "img": "https://files.maplabs.io/bridge/usdt.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 4,
                "tokenId": "MintToken",
                "address": "0x4FeF2283a7012021e43Bae13aaAEBE9B638D5c10",
                "name": "MintToken",
                "chainId": 3,
                "isMint": 1,
                "symbol": "MTTK",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/idv.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 7,
                "tokenId": "MAP",
                "address": "0x47f423C44976Fbe745588020b85B09A56458f9C0",
                "name": "MAP",
                "chainId": 3,
                "isMint": 0,
                "symbol": "MAP",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/map.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 13,
                "tokenId": "ETH",
                "address": "0x0000000000000000000000000000000000000000",
                "name": "ETH",
                "chainId": 3,
                "isMint": 0,
                "symbol": "ETH",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/eth.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            }
        ],
            "22776": [
            {
                "id": 2,
                "tokenId": "USDT",
                "address": "0xd505bfDB4f7FE74D4FfA0D641eDdFBe3A344a671",
                "name": "USDT",
                "chainId": 22776,
                "isMint": 0,
                "symbol": "USDT",
                "decimal": 6,
                "img": "https://files.maplabs.io/bridge/usdt.png",
                "vaultToken": "0x67F5C955CAe94B4e74277F87F6A56FC84502a7E4",
                "vaultDecimal": 6,
                "pid": 7
            },
            {
                "id": 5,
                "tokenId": "MintToken",
                "address": "0xd6a9f83A761D79d2448F0EB347E2CbF7c227bd6d",
                "name": "MintToken",
                "chainId": 22776,
                "isMint": 1,
                "symbol": "MTTK",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/idv.png",
                "vaultToken": "0xf798b5c26b6C8F8687006f39BD5BE6fB981a9D54",
                "vaultDecimal": 18,
                "pid": 2
            },
            {
                "id": 8,
                "tokenId": "MAP",
                "address": "0x0000000000000000000000000000000000000000",
                "name": "MAP",
                "chainId": 22776,
                "isMint": 0,
                "symbol": "MAP",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/map.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            },
            {
                "id": 14,
                "tokenId": "ETH",
                "address": "0xD609c958111F46a9B0128C08E906EAF54F5E028a",
                "name": "ETH",
                "chainId": 22776,
                "isMint": 0,
                "symbol": "ETH",
                "decimal": 18,
                "img": "https://files.maplabs.io/bridge/eth.png",
                "vaultToken": null,
                "vaultDecimal": null,
                "pid": null
            }
        ]
    }
}
}
```

### queryChainList

Uri: /api/queryChainList

Request method: post

Returns supported chains.

##### Parameters

none

##### Returns

`list` - List of chains:
- `chain`: Chain identification.
- `contract`: Cross address.
- `rpc`: Rpc url.
- `chainId`: Chain Id.
- `gasLimit`: Gas limit.
- `chainName`: Chain name.
- `chainImg`: Chain icon.
- `scanUrl`: Scan url.

##### Example
```js
// Request
curl --location --request POST 'http://18.139.224.21:8201/api/queryChainList'
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": 1,
            "chain": "MAP",
            "contract": "0x8084d0C99217221a8d233B3162C724F676295982",
            "rpc": "https://poc2-rpc.maplabs.io/",
            "chainId": 22776,
            "gasLimit": "600000",
            "chainName": "MAP Makalu",
            "chainImg": "https://files.maplabs.io/bridge/map.png",
            "scanUrl": "https://makalu.mapscan.io/",
            "volume": null,
            "tokenAddress": null,
            "decimal": null,
            "liquidity": null
        },
        {
            "id": 2,
            "chain": "ETH",
            "contract": "0x1f027B5B09924AC420002C1d09F7241c2041045A",
            "rpc": "https://ropsten.infura.io/v3/7d01bfb33f364de08d1377e3d1a843b2",
            "chainId": 3,
            "gasLimit": "600000",
            "chainName": "Ethereum Rospten",
            "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/ETH.png",
            "scanUrl": "https://ropsten.etherscan.io/",
            "volume": null,
            "tokenAddress": null,
            "decimal": null,
            "liquidity": null
        },
        {
            "id": 3,
            "chain": "BSC",
            "contract": "0x7A3504932E9AE869057418b333d4f25330a9355A",
            "rpc": "https://data-seed-prebsc-2-s2.binance.org:8545/",
            "chainId": 97,
            "gasLimit": "600000",
            "chainName": "BSC Testnet",
            "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/BSC.png",
            "scanUrl": "https://testnet.bscscan.com/",
            "volume": null,
            "tokenAddress": null,
            "decimal": null,
            "liquidity": null
        },
        {
            "id": 4,
            "chain": "POLYGON",
            "contract": "0xbfc8DB8C15c5495aD109176E2c09f1Fb6d500953",
            "rpc": "https://rpc-mumbai.maticvigil.com/",
            "chainId": 80001,
            "gasLimit": "600000",
            "chainName": "MATIC Testnet",
            "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/Polygon.png",
            "scanUrl": "https://mumbai.polygonscan.com/",
            "volume": null,
            "tokenAddress": null,
            "decimal": null,
            "liquidity": null
        }
    ]
}
}
```