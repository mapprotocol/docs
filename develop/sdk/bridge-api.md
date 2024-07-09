
## Bridge Scan API Reference

List of APIs

- [queryCrossInfoById](#queryCrossInfoById)
- [queryCrossInfoBySourceHash](#queryCrossInfoBySourceHash)
- [queryHistoryByChainIdAddress](#queryHistoryByChainIdAddress)


### queryCrossInfoById

Uri: /api/queryCrossInfoById

Request method: GET

Return cross information

##### Parameters

Parameter Type：

`id` - Interger of transaction id.

Example:

http://host:port/api/queryCrossInfoById?id=322111

##### Returns

`sourceInfo`
- `address`: source address
- `chainInfo` : source chain info json object, include chainId, chainName, scanUrl, chainImg
- `contract` : contract address
- `block` : block number
- `hash` : source chain transaction hash
- `timestamp` : source transaction timestamp

`relyerInfo`
- `address`: relyer address
- `chainInfo` : relyer chain info json object, include chainId, chainName, scanUrl, chainImg
- `contract` : contract address
- `block` : block number
- `hash` : relyer chain transaction hash
- `timestamp` : relyer transaction timestamp

`destinationInfo`
- `address`: destination address
- `chainInfo` : destination chain info json object, include chainId, chainName, scanUrl, chainImg
- `contract` : contract address
- `block` : block number
- `hash` : destination chain transaction hash
- `timestamp` : destination transaction timestamp



`state` - Bridge Status. 0: crossing 1 completed 2 relayering 3 relayer completed

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://bn-api.chainservice.io/api/queryCrossInfoById?id=322111"
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "sourceInfo": {
        "address": "0x14a5b11be1f996a2c662f8968d017328b4b80c3a",
            "chainInfo": {
            "id": 5,
                "chainId": "137",
                "chainName": "Polygon",
                "scanUrl": "https://polygonscan.com/",
                "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/Polygon.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
        },
        "contract": "0xbb21e441fb738f54e6ec244e435475096e179d66",
            "block": 59127917,
            "hash": "0xdd15df0239d4b58cf419e2e4243555fe4d862fa4caf154086222dadd3d751013",
            "timestamp": "2024-07-09T00:01:28.000+00:00"
    },
    "destinationInfo": {
        "address": "0xa97505a93bf036f87ff398d851c06946b6ffd849",
            "chainInfo": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Protocol",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
        },
        "contract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
            "block": 12663543,
            "hash": "0x8e48df42c3ae18f834da59867048d313c167a3149bac9bfa62c04b78d1df36d7",
            "timestamp": "2024-07-09T00:03:08.000+00:00"
    },
    "relyerInfo": {
        "address": "0xa97505a93bf036f87ff398d851c06946b6ffd849",
            "chainInfo": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Protocol",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
        },
        "contract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
            "block": 12663543,
            "hash": "0x8e48df42c3ae18f834da59867048d313c167a3149bac9bfa62c04b78d1df36d7",
            "timestamp": "2024-07-09T00:03:08.000+00:00"
    },
    "state": 1
}
}
```



### queryCrossInfoBySourceHash

Uri: /api/queryCrossInfoBySourceHash

Request method: GET

Return cross information

##### Parameters

Parameter Type：

`txHash` - String of transaction hash.

Example:

http://host:port/api/queryCrossInfoBySourceHash?txHash=0x939a86a02c9cb6a52cf6d4ecbe897ea3924ba9a5a8a588a05390904ba7ea1ddb

##### Returns
`list`

-`sourceInfo`
 - `address`: source address
 - `chainInfo` : source chain info json object, include chainId, chainName, scanUrl, chainImg
 - `contract` : contract address
 - `block` : block number
 - `hash` : source chain transaction hash
 - `timestamp` : source transaction timestamp

-`relyerInfo`
- `address`: relyer address
- `chainInfo` : relyer chain info json object, include chainId, chainName, scanUrl, chainImg
- `contract` : contract address
- `block` : block number
- `hash` : relyer chain transaction hash
- `timestamp` : relyer transaction timestamp

-`destinationInfo`
- `address`: destination address
- `chainInfo` : destination chain info json object, include chainId, chainName, scanUrl, chainImg
- `contract` : contract address
- `block` : block number
- `hash` : destination chain transaction hash
- `timestamp` : destination transaction timestamp
 
 

`state` - Bridge Status. 0: crossing 1 completed 2 relayering 3 relayer completed

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://bn-api.chainservice.io/api/queryCrossInfoBySourceHash?txHash=0xdd15df0239d4b58cf419e2e4243555fe4d862fa4caf154086222dadd3d751013"
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "sourceInfo": {
        "address": "0x14a5b11be1f996a2c662f8968d017328b4b80c3a",
            "chainInfo": {
            "id": 5,
                "chainId": "137",
                "chainName": "Polygon",
                "scanUrl": "https://polygonscan.com/",
                "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/Polygon.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
        },
        "contract": "0xbb21e441fb738f54e6ec244e435475096e179d66",
            "block": 59127917,
            "hash": "0xdd15df0239d4b58cf419e2e4243555fe4d862fa4caf154086222dadd3d751013",
            "timestamp": "2024-07-09T00:01:28.000+00:00"
    },
    "destinationInfo": {
        "address": "0xa97505a93bf036f87ff398d851c06946b6ffd849",
            "chainInfo": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Protocol",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
        },
        "contract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
            "block": 12663543,
            "hash": "0x8e48df42c3ae18f834da59867048d313c167a3149bac9bfa62c04b78d1df36d7",
            "timestamp": "2024-07-09T00:03:08.000+00:00"
    },
    "relyerInfo": {
        "address": "0xa97505a93bf036f87ff398d851c06946b6ffd849",
            "chainInfo": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Protocol",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
        },
        "contract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
            "block": 12663543,
            "hash": "0x8e48df42c3ae18f834da59867048d313c167a3149bac9bfa62c04b78d1df36d7",
            "timestamp": "2024-07-09T00:03:08.000+00:00"
    },
    "state": 1
}
}
```


### queryHistoryByChainIdAddress

Uri: /api/queryHistoryByChainIdAddress

Request method: GET

Get cross history by chain id and address.

##### Parameters

Parameter Type：

`chainId` - chain id.
`address` - address.
`page` - page, default 1.
`size` - size, default 10.

Example:

http://host:port/api/queryHistoryByChainIdAddress?chainId=137&address=0x14a5b11be1f996a2c662f8968d017328b4b80c3a


##### Returns

`list` - list info
 - id : transfer if
 - sourceAddress : source address
 - sourceHash : source hash
 - sourceHeight : source height
 - sourceTimestamp : transfer timestamp
 - sourceChainInfo : source chain info json object, include chainId, chainName, scanUrl, chainImg
 - relayHash : relay hash
 - relayHeight : relay height
 - relayChainInfo : relay chain info json object, include chainId, chainName, scanUrl, chainImg
 - destinationAddress : destination address
 - destinationHash : destination hash
 - destinationHeight : destination height
 - destinationInfo : destination chain info json object, include chainId, chainName, scanUrl, chainImg
`state` - Bridge Status. 0: crossing 1 completed 2 relayering 3 relayer completed

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://bn-api.chainservice.io/api/queryHistoryByChainIdAddress?address=0x14a5b11be1f996a2c662f8968d017328b4b80c3a&chainId=137&page=1&size=10"
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": 322111,
            "sourceAddress": "0x14a5b11be1f996a2c662f8968d017328b4b80c3a",
            "sourceHash": "0xdd15df0239d4b58cf419e2e4243555fe4d862fa4caf154086222dadd3d751013",
            "sourceHeight": 59127917,
            "sourceTimestamp": "2024-07-09T00:01:28.000+00:00",
            "sourceChainInfo": {
                "id": 5,
                "chainId": "137",
                "chainName": "Polygon",
                "scanUrl": "https://polygonscan.com/",
                "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/Polygon.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
            },
            "relayHash": null,
            "relayHeight": null,
            "relayChainInfo": null,
            "destinationHash": "0x8e48df42c3ae18f834da59867048d313c167a3149bac9bfa62c04b78d1df36d7",
            "destinationHeight": 12663543,
            "destinationChainInfo": {
                "id": 1,
                "chainId": "22776",
                "chainName": "MAP Protocol",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
            }
        },
        {
            "id": 322082,
            "sourceAddress": "0x14a5b11be1f996a2c662f8968d017328b4b80c3a",
            "sourceHash": "0x326cfe03b637e19de141d25556e5f9f0c24d3fea30982432ef17761029c741e7",
            "sourceHeight": 59124846,
            "sourceTimestamp": "2024-07-08T22:12:35.000+00:00",
            "sourceChainInfo": {
                "id": 5,
                "chainId": "137",
                "chainName": "Polygon",
                "scanUrl": "https://polygonscan.com/",
                "chainImg": "https://get.celer.app/cbridge-icons/chain-icon/Polygon.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
            },
            "relayHash": null,
            "relayHeight": null,
            "relayChainInfo": null,
            "destinationHash": "0x60d85e2c625fe28df7bacddeb9add08702520f59aeb916b403853d0ac7902e10",
            "destinationHeight": 12662231,
            "destinationChainInfo": {
                "id": 1,
                "chainId": "22776",
                "chainName": "MAP Protocol",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0xfeb2b97e4efce787c08086dc16ab69e063911380",
                "color": null
            }
        }
    ],
        "total": null
}
}
```