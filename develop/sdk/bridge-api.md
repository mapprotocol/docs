
## Bridge Scan API Reference

List of APIs

- [queryCrossList](#queryCrossList)
- [queryCrossInfo](#queryCrossInfo)
- [queryBridgeHistoryByAddress](#queryBridgeHistoryByAddress)
- [queryBridgeInfoById](#queryBridgeInfoById)
- [queryBridgeInfoBySourceHash](#queryBridgeInfoBySourceHash)
- [queryCrossInfoByOrderId](#queryCrossInfoByOrderId)
- [queryMessageBridge](#queryMessageBridge)


### queryCrossList

Uri: /api/queryCrossList

Request method: GET

Return bridge information, you can query the source address, source hash, source chain info, relayer hash, relayer chain info, destination hash, destination chain info, bridge state, bridge times

##### Parameters

Parameter Type：

`page` - Integer of page number, default is 1.

`sise` - Integer of page size, default is 10.

`txHash` - String of transaction hash.

`sourceAddress` - String of source address.

`sourceChainId` - String of source chain id.

`destinationChainId` - String of destination chain id.

`orderId` - String of order id.

Example:

http://host:port/api/queryCrossList?page=1&size=10&txHash=0x939a86a02c9cb6a52cf6d4ecbe897ea3924ba9a5a8a588a05390904ba7ea1ddb

##### Returns


`id` - id

`address` - Source Address

`state` - Bridge Status. 0: crossing 1 completed 2 relayering 3 relayer completed

`sourceHash` - Source Transactions Hash

`relayerHash` - Relayer Transactions Hash

`toHash` - Destination Transactions Hash

`sourceChain` - Source Chain Object, {id, chainId, chainName, scanUrl, chainImg}

`relayerChain` - Relayer Chain Object, {id, chainId, chainName, scanUrl, chainImg}

`toChain` - Destination Chain Object, {id, chainId, chainName, scanUrl, chainImg}

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryCrossList?page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": 85441,
            "address": "0x0230d02bf0b1757ea7fe658174868c84d99ca3ca",
            "state": 1,
            "sourceHash": "0xb9f5dbda5a4accbea8608aa384480071eace402b42382532036172ea06c0b971",
            "relayerHash": "0xb9f5dbda5a4accbea8608aa384480071eace402b42382532036172ea06c0b971",
            "toHash": "0x4f6bb57e4f207a9c61cc50725a71ec12bb25da848eef51d58facf5d96e0127f6",
            "sourceChain": {
                "id": 1,
                "chainId": 22776,
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
                "color": null
            },
            "relayerChain": {
                "id": 1,
                "chainId": 22776,
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
                "color": null
            },
            "toChain": {
                "id": 3,
                "chainId": 56,
                "chainName": "BNB Chain",
                "scanUrl": "https://bscscan.com/",
                "chainImg": "https://files.maplabs.io/bridge/bsc.png",
                "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
                "color": null
            },
            "timestamp": "2023-07-27 07:50:13"
        },
        {
            "id": 85440,
            "address": "0xd2fd91bca179d8b16b3de23b6ae8c0878dd271ad",
            "state": 1,
            "sourceHash": "0x50e9bbd3c5bb803f13ef5f18557747129e524b87f8e095186198021fbdb4d35b",
            "relayerHash": "0x50e9bbd3c5bb803f13ef5f18557747129e524b87f8e095186198021fbdb4d35b",
            "toHash": "4vSgv8ygqb9ZvPPvpfzNyi674MK3MqxbA6x3tp2ZQJo1",
            "sourceChain": {
                "id": 1,
                "chainId": 22776,
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
                "color": null
            },
            "relayerChain": {
                "id": 1,
                "chainId": 22776,
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
                "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
                "color": null
            },
            "toChain": {
                "id": 4,
                "chainId": 1360100178526209,
                "chainName": "Near Protocol",
                "scanUrl": "https://explorer.near.org/",
                "chainImg": "https://files.maplabs.io/image/maponimn.png",
                "mosContract": "mos.mfac.butternetwork.near",
                "color": null
            },
            "timestamp": "2023-07-27 07:48:58"
        }
        ....
    ],
        "total": 85188
}
```


### queryCrossInfo

Uri: /api/queryCrossInfo

Request method: GET

Get bridge info by id.

##### Parameters

Parameter Type：

`id` - id.

Example:

http://host:port/api/queryCrossInfo?id=1


##### Returns

`sourceAddress` - Source Address.

`sourceHash` - Source Transactions Hash.

`sourceHeight` - Source Transactions Height.

`sourceInput` - Source Transactions Input.

`sourceNowHeight` - Source Chain Now Height.

`sourceChainDict` - Source Chain Object, {id, chainId, chainName, scanUrl, chainImg}.

`sourceMOSContract` - Source Mos Address.

`relayerOutHash` - Relayer Transactions Hash.

`relayerOutHeight` -  Relayer Transactions Height.

`relayerOutInput` - Relayer Transactions Input.

`relayerLightNode` - Relayer Light Node Address.

`relayerNowHeight` - Relayer Chain Now Height.

`relayerVerifyState` - Relayer Verify Status.

`relayerChainDict` - Relayer Chain Object, {id, chainId, chainName, scanUrl, chainImg}.

`relayerMOSContract` - Relayer Mos Address.

`relayerMessengerContract` - Relayer Messenger Address.

`destinationHash` - Destination Transactions Hash.

`destinationHeight` - Destination Transactions Height.

`destinationInput` - Destination Transactions Input.

`toLightNode` - Destination Light Node Address.

`toNowHeight` - Destination Chain Now Height.

`destinationVerifyState` - Destination Verify status.

`destinationChainDict` - Destination Chain Object, {id, chainId, chainName, scanUrl, chainImg}.

`destinationMOSContract` - Destination MOS Address.

`destinationMessengerContract` - Destination Messenger Address.

`state` - Bridge Status. 0: crossing 1 completed 2 relayering 3 relayer completed

`timestamp` - Bridge times.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryCrossInfo?id=85454"
// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "bridgeInfo": {
        "sourceAddress": "0xf580b2b40ca7530c6a92c2463aa810f0b6dd75dd",
        "sourceHash": "0xca72da9c66541236a706586b97ddda92b60eb2abac4c70d801873fa387614e53",
        "sourceHeight": 6733770,
        "relayerOutHeight": 6733770,
        "relayerOutHash": "0xca72da9c66541236a706586b97ddda92b60eb2abac4c70d801873fa387614e53",
        "destinationHash": "2XyRLYvEL8NMCKXc6uzzVQe6CB8yqiiovxPNaSUtF8tj",
        "destinationHeight": 97440622,
        "sourceInput": "0x981aff4e0000000000000000000000009f722b2cb30093f766221fd0d37964949ed6691800000000000000000000000000000000000000000000000000000000000000800000000000000000000000000000000000000000000000039be42c06efac80000000000000000000000000000000000000000000000000000004d50100000001000000000000000000000000000000000000000000000000000000000000004034633734366335383463313262633736333766376434356262303433383832616631376430613439373337353536613131363138653931383337303736636364",
        "relayerOutInput": "0x981aff4e0000000000000000000000009f722b2cb30093f766221fd0d37964949ed6691800000000000000000000000000000000000000000000000000000000000000800000000000000000000000000000000000000000000000039be42c06efac80000000000000000000000000000000000000000000000000000004d50100000001000000000000000000000000000000000000000000000000000000000000004034633734366335383463313262633736333766376434356262303433383832616631376430613439373337353536613131363138653931383337303736636364",
        "destinationInput": "{\"FunctionCall\":{\"args\":\"eyJpbmRleCI6NiwicmVjZWlwdF9wcm9vZiI6eyJhZ2dfcGsiOnsieGkiOiIweDI1OWYzNWQ4NTQ4MWEwMzI0NWNmYmM2MGM2YjVjOTNjZTJmYzgxZjYxNzc4YzZkMDM4NWZmYjY2M2ZhM2IyYzEiLCJ4ciI6IjB4MWJlNGYzNThjNDQ5MTU1MDYyNzM4MmE3NzFlOTVjNDc4MjAyYTZkYmE3ZDY4NDc5ZWZmMzNhNWE2YTdkMWY3NiIsInlpIjoiMHgwOGE0OWMyN2EwMWJiMjBhN2U1OTdiMTA1OWQ4Y2YxNGM4MzYwZWMxYTQ1M2YyMjE5NGRiMmNiNzkyMjg2ZmFhIiwieXIiOiIweDJiMWU5ZDZjZDdkNTdlMzRlOWRmZjJhY2U1NjE4ZGEwYWM2MzNlM2QxOWM1YjQ5NTk2MGJlYTViMTUwMjU2NGIifSwiaGVhZGVyIjp7InBhcmVudEhhc2giOiIweGIyYzEwYzhjMzUwMTY2MTMzMGFjNzhjNWY0M2I0ZjI1MTU5OTE4ZjIwNzlmMDUzODczYTE1ODQ0NjEyMjRmNzMiLCJjb2luYmFzZSI6IjB4N2U0NzBlMDAxNTYwZGU0NjI1ZWIyZmQ4MzMyMTE5NjU0MzMxNjkyNCIsInJvb3QiOiIweDE3ZGUzNTk5ZTJmNTlkOTA5NzAzMmE1ODI2ODhiNTdmNWY3OWZhMmVmNTc4N2RlNWYzNGM0ZTc2MDhmYjRhMDUiLCJ0eEhhc2giOiIweDllNzYzYmY2ZTU0OTYwYjUwZjFmMWQ5MTQzZDMwMDk0MTRlMjUzMmJkNjQzODE5MTYwOTFjOGE2MzA1MTAzNzEiLCJyZWNlaXB0SGFzaCI6IjB4ZDBmYTZhMjUzYWZhOWVhOTM5MmRjNWMwMDRlOWQ3NjEyN2ExNjc4MTAwODI1MTNmYWY2NjAzMzAyMjJjMDBjOCIsImJsb29tIjoiMHgwMDAwMDAwMDAwMDAyMDAyMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDQwMDAwMDAwMDAwMDAwMDAwMTAwMDAwMDAwMDAwMDAwMDAwMDA4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNDAwMDAwMDAwMDAwMDIwMDAwMDIwMDEwMDAwMDAwMDAwMDIwMjAwMDAwMDAwMDAwODAwMDAwMDIwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAyMDAwMDAwMDAwMDEwMDAwMDAwMDgwMDAwMDAwMDAwMDIwMDAwMDAwMDA0MDAxMDAwMDEwMDAwMDAwMjAwMjAwMDAwMDAwMDAwMDAwMDA4MDAwMDAwMjgwMDAwMDAwMDAwMDAwMDAwMDAwMDIwMDA0MDAwMDAwMDAyMDAwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMDAwNDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTA0MDAxMDAwMDAwMjAwMDAwMDAwMDIwMDAwMDAwMDAwMDAwMDgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAyNDgwMDAxMDQwMDAwMDAwMDAwMDAwMDAyMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMCIsIm51bWJlciI6IjB4NjZiZmNhIiwiZ2FzTGltaXQiOiIweGM2NWQ0MCIsImdhc1VzZWQiOiIweDI5Y2JmIiwidGltZSI6IjB4NjRjMjJkYmQiLCJleHRyYSI6IjB4ZDY4MzAxMDEwMzg0Njc2NTc0Njg4NjY3NmYzMTJlMzIzMDg1NmM2OTZlNzU3ODAwMDAwMDAwMDAwMDAwMDAwMGY4ZGRjMGMwYzA4MGI4NDFmZjcwY2ViNzc2Njc5MzBmNDU0MmM0MDJhMDkwY2IxNjc1NWE0NzcyM2YxZjMzYTc2YTU1NWIyM2I1YzZmZmIyNjhhNjJlM2U1ZmIxMjFjODQ4NGE3NmFhZmFhMzg5NDgxM2MzY2FjOTU5MzdlMDdhYWQxMjI0Y2JjNGRhNzY2YTAxZjg0OTg1MDU2ODVmZmY4ZmI4NDAwNDA0Zjk4YmNiOTMwMGUxODA2ZGQ1MTJjMjJiYWMxMDlkOTNkNDhjZjkzMmZjYzM4MTc2NWZiOGJjMTNlYTJkMTAzMWUzNTFhOTc0MWY2ZjcyN2Q3YzJmYzA1NTU4NWU0YTUxNWU5ZDQyNWZmZmEwYTYyMDNlMGY1YTkwZGQ0ZjgwZjg0OTg1MDU5ZmZmZTFmZmI4NDAxYzZkZGRkMjQ1ZjllZGI3OWNhODM4NGFmMzUyZDJkOWZjZWIzYmFkOTFlODI3MmJkNDAyMDA5NzMzMGI2YTA0MmRkOTVmZDFkYzE2ZDIyNmVmYzRiNTE0NTljMWVkZTNlOTAxOTZlOTZiY2VmODBhZmRjMDk5YzVhNTFkZWViYzgwIiwibWl4RGlnZXN0IjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwIiwibm9uY2UiOiIweDAwMDAwMDAwMDAwMDAwMDAiLCJiYXNlRmVlIjoiMHgxNzQ4NzZlODAwIiwiaGFzaCI6IjB4OTgwMDgzOWYxNDFlOGY3NzQ0ZDNhZTUzODg1ZjQ0ZmUyZTkxZTQ3OTgwMWZjMmY4N2Q5M2ExMTdiNjA0MTFlNiJ9LCJrZXlfaW5kZXgiOiIweDgwIiwicHJvb2YiOlsiMHhmOTA4NjY4MjIwODBiOTA4NjAwMmY5MDg1YzAxODMwMjljYmZiOTAxMDAwMDAwMDAwMDAwMDAyMDAyMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDQwMDAwMDAwMDAwMDAwMDAwMTAwMDAwMDAwMDAwMDAwMDAwMDA4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNDAwMDAwMDAwMDAwMDIwMDAwMDIwMDEwMDAwMDAwMDAwMDIwMjAwMDAwMDAwMDAwODAwMDAwMDIwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAyMDAwMDAwMDAwMDEwMDAwMDAwMDgwMDAwMDAwMDAwMDIwMDAwMDAwMDA0MDAxMDAwMDEwMDAwMDAwMjAwMjAwMDAwMDAwMDAwMDAwMDA4MDAwMDAwMjgwMDAwMDAwMDAwMDAwMDAwMDAwMDIwMDA0MDAwMDAwMDAyMDAwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMDAwNDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTA0MDAxMDAwMDAwMjAwMDAwMDAwMDIwMDAwMDAwMDAwMDAwMDgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAyNDgwMDAxMDQwMDAwMDAwMDAwMDAwMDAyMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMGY5MDc1MWY4OWI5NDlmNzIyYjJjYjMwMDkzZjc2NjIyMWZkMGQzNzk2NDk0OWVkNjY5MThmODYzYTBkZGYyNTJhZDFiZTJjODliNjljMmIwNjhmYzM3OGRhYTk1MmJhN2YxNjNjNGExMTYyOGY1NWE0ZGY1MjNiM2VmYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDBmNTgwYjJiNDBjYTc1MzBjNmE5MmMyNDYzYWE4MTBmMGI2ZGQ3NWRkYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA2MzAxMDUxODljNzExNDY2N2E3MTc5YWE1N2YwNzY0N2E1ZjQyYjdmYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDM5YmU0MmMwNmVmYWM4MDAwZjg5Yjk0OWY3MjJiMmNiMzAwOTNmNzY2MjIxZmQwZDM3OTY0OTQ5ZWQ2NjkxOGY4NjNhMDhjNWJlMWU1ZWJlYzdkNWJkMTRmNzE0MjdkMWU4NGYzZGQwMzE0YzBmN2IyMjkxZTViMjAwYWM4YzdjM2I5MjVhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMGY1ODBiMmI0MGNhNzUzMGM2YTkyYzI0NjNhYTgxMGYwYjZkZDc1ZGRhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDYzMDEwNTE4OWM3MTE0NjY3YTcxNzlhYTU3ZjA3NjQ3YTVmNDJiN2ZhMGZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZWRmZTQwNzhiYjIwYzNlYWE3ZmZmOTAxYmQ5NDYzMDEwNTE4OWM3MTE0NjY3YTcxNzlhYTU3ZjA3NjQ3YTVmNDJiN2ZmODYzYTBlMjMyNDdiMTBlNWNmMmUyYTZhMTMzNTI1OThmYWUyNWM4ODk3MzFmM2I1ZWZmNWZiNTIxODRlMTIxYjQxZDU0YTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA1OGY4YTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA0ZDUwMTAwMDAwMDAxYjkwMTQwZGNiNjE2MmMxNjMyODA1M2EwNjE5ZDg3NjZiY2M2MTM2MzczMTJlY2MzNWVkN2RmY2M4ZTI4Njg2YjI2ZDk5YTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDlmNzIyYjJjYjMwMDkzZjc2NjIyMWZkMGQzNzk2NDk0OWVkNjY5MTgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMGEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDBlMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMzliZTQyYzA2ZWZhYzgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDE0ZjU4MGIyYjQwY2E3NTMwYzZhOTJjMjQ2M2FhODEwZjBiNmRkNzVkZDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNDAzNDYzMzczNDM2NjMzNTM4MzQ2MzMxMzI2MjYzMzczNjMzMzc2NjM3NjQzNDM1NjI2MjMwMzQzMzM4MzgzMjYxNjYzMTM3NjQzMDYxMzQzOTM3MzMzNzM1MzUzNjYxMzEzMTM2MzEzODY1MzkzMTM4MzMzNzMwMzczNjYzNjM2NGY4OWI5NDlmNzIyYjJjYjMwMDkzZjc2NjIyMWZkMGQzNzk2NDk0OWVkNjY5MThmODYzYTBkZGYyNTJhZDFiZTJjODliNjljMmIwNjhmYzM3OGRhYTk1MmJhN2YxNjNjNGExMTYyOGY1NWE0ZGY1MjNiM2VmYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA2MzAxMDUxODljNzExNDY2N2E3MTc5YWE1N2YwNzY0N2E1ZjQyYjdmYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDBmYzhkNTRhODBlMGZhY2M1MWQ2Y2I4ZGQyZGY2MTg1MTkzMmY4ZGVmYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTNmYmU4NWVkYzkwMDAwZjg5Yjk0NjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZmY4NjNhMDcwODEzYzlkY2E3MDIwNDU4MDk1ZDgyNTFkMmExMjY4Y2ZhZGY3ZWI4YTFmZDIwNTRjNTcxZmNhNDZmZWE5MDZhMGRjYjYxNjJjMTYzMjgwNTNhMDYxOWQ4NzY2YmNjNjEzNjM3MzEyZWNjMzVlZDdkZmNjOGUyODY4NmIyNmQ5OWFhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDlmNzIyYjJjYjMwMDkzZjc2NjIyMWZkMGQzNzk2NDk0OWVkNjY5MThhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxNjM0NTc4NWQ4YTAwMDBmODliOTQ5ZjcyMmIyY2IzMDA5M2Y3NjYyMjFmZDBkMzc5NjQ5NDllZDY2OTE4Zjg2M2EwZGRmMjUyYWQxYmUyYzg5YjY5YzJiMDY4ZmMzNzhkYWE5NTJiYTdmMTYzYzRhMTE2MjhmNTVhNGRmNTIzYjNlZmEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZmEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMGEwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAzOWE4MGU2OGU5MjIyODAwMGY5MDI3ZDk0NjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZmY4NjNhMDQ0ZmY3NzAxODY4OGRhZDRiMjQ1ZThhYjk3MzU4ZWQ1N2VkOTIyNjk5NTJlY2U3ZmZkMzIxMzY2Y2UwNzg2MjJhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDU4ZjhhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDRkNTAxMDAwMDAwMDFiOTAyMDBkY2I2MTYyYzE2MzI4MDUzYTA2MTlkODc2NmJjYzYxMzYzNzMxMmVjYzM1ZWQ3ZGZjYzhlMjg2ODZiMjZkOTlhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDBjMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTQwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwM2Y2NGRhODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDE0OWY3MjJiMmNiMzAwOTNmNzY2MjIxZmQwZDM3OTY0OTQ5ZWQ2NjkxODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTRmNTgwYjJiNDBjYTc1MzBjNmE5MmMyNDYzYWE4MTBmMGI2ZGQ3NWRkMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA0MDM0NjMzNzM0MzY2MzM1MzgzNDYzMzEzMjYyNjMzNzM2MzMzNzY2Mzc2NDM0MzU2MjYyMzAzNDMzMzgzODMyNjE2NjMxMzc2NDMwNjEzNDM5MzczMzM3MzUzNTM2NjEzMTMxMzYzMTM4NjUzOTMxMzgzMzM3MzAzNzM2NjM2MzY0MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAzYzYxMzA2MjM4MzYzOTM5MzE2MzM2MzIzMTM4NjIzMzM2NjMzMTY0MzEzOTY0MzQ2MTMyNjUzOTY1NjIzMDYzNjUzMzM2MzAzNjY1NjIzNDM4MmU2NjYxNjM3NDZmNzI3OTJlNjI3MjY5NjQ2NzY1MmU2ZTY1NjE3MjAwMDAwMDAwIl0sInJlY2VpcHQiOnsicmVjZWlwdF90eXBlIjoiMiIsInBvc3Rfc3RhdGVfb3Jfc3RhdHVzIjoiMHgwMSIsImN1bXVsYXRpdmVfZ2FzX3VzZWQiOiIxNzExOTkiLCJibG9vbSI6IjB4MDAwMDAwMDAwMDAwMjAwMjAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA0MDAwMDAwMDAwMDAwMDAwMDEwMDAwMDAwMDAwMDAwMDAwMDAwODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDQwMDAwMDAwMDAwMDAyMDAwMDAyMDAxMDAwMDAwMDAwMDAyMDIwMDAwMDAwMDAwMDgwMDAwMDAyMDAwMDAxMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMjAwMDAwMDAwMDAxMDAwMDAwMDA4MDAwMDAwMDAwMDAyMDAwMDAwMDAwNDAwMTAwMDAxMDAwMDAwMDIwMDIwMDAwMDAwMDAwMDAwMDAwODAwMDAwMDI4MDAwMDAwMDAwMDAwMDAwMDAwMDAyMDAwNDAwMDAwMDAwMjAwMDAwMDAxMDAwMDAwMDAwMDAwMDAwMDAwMDQwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDEwNDAwMTAwMDAwMDIwMDAwMDAwMDAyMDAwMDAwMDAwMDAwMDA4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMjQ4MDAwMTA0MDAwMDAwMDAwMDAwMDAwMjAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxMDAwMDAwMDAwMDAwMDAwMDAiLCJsb2dzIjpbeyJhZGRyZXNzIjoiMHg5ZjcyMmIyY2IzMDA5M2Y3NjYyMjFmZDBkMzc5NjQ5NDllZDY2OTE4IiwidG9waWNzIjpbIjB4ZGRmMjUyYWQxYmUyYzg5YjY5YzJiMDY4ZmMzNzhkYWE5NTJiYTdmMTYzYzRhMTE2MjhmNTVhNGRmNTIzYjNlZiIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwZjU4MGIyYjQwY2E3NTMwYzZhOTJjMjQ2M2FhODEwZjBiNmRkNzVkZCIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZiJdLCJkYXRhIjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDM5YmU0MmMwNmVmYWM4MDAwIn0seyJhZGRyZXNzIjoiMHg5ZjcyMmIyY2IzMDA5M2Y3NjYyMjFmZDBkMzc5NjQ5NDllZDY2OTE4IiwidG9waWNzIjpbIjB4OGM1YmUxZTVlYmVjN2Q1YmQxNGY3MTQyN2QxZTg0ZjNkZDAzMTRjMGY3YjIyOTFlNWIyMDBhYzhjN2MzYjkyNSIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwZjU4MGIyYjQwY2E3NTMwYzZhOTJjMjQ2M2FhODEwZjBiNmRkNzVkZCIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZiJdLCJkYXRhIjoiMHhmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmVkZmU0MDc4YmIyMGMzZWFhN2ZmIn0seyJhZGRyZXNzIjoiMHg2MzAxMDUxODljNzExNDY2N2E3MTc5YWE1N2YwNzY0N2E1ZjQyYjdmIiwidG9waWNzIjpbIjB4ZTIzMjQ3YjEwZTVjZjJlMmE2YTEzMzUyNTk4ZmFlMjVjODg5NzMxZjNiNWVmZjVmYjUyMTg0ZTEyMWI0MWQ1NCIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNThmOCIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNGQ1MDEwMDAwMDAwMSJdLCJkYXRhIjoiMHhkY2I2MTYyYzE2MzI4MDUzYTA2MTlkODc2NmJjYzYxMzYzNzMxMmVjYzM1ZWQ3ZGZjYzhlMjg2ODZiMjZkOTlhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwOWY3MjJiMmNiMzAwOTNmNzY2MjIxZmQwZDM3OTY0OTQ5ZWQ2NjkxODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMGUwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAzOWJlNDJjMDZlZmFjODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTRmNTgwYjJiNDBjYTc1MzBjNmE5MmMyNDYzYWE4MTBmMGI2ZGQ3NWRkMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA0MDM0NjMzNzM0MzY2MzM1MzgzNDYzMzEzMjYyNjMzNzM2MzMzNzY2Mzc2NDM0MzU2MjYyMzAzNDMzMzgzODMyNjE2NjMxMzc2NDMwNjEzNDM5MzczMzM3MzUzNTM2NjEzMTMxMzYzMTM4NjUzOTMxMzgzMzM3MzAzNzM2NjM2MzY0In0seyJhZGRyZXNzIjoiMHg5ZjcyMmIyY2IzMDA5M2Y3NjYyMjFmZDBkMzc5NjQ5NDllZDY2OTE4IiwidG9waWNzIjpbIjB4ZGRmMjUyYWQxYmUyYzg5YjY5YzJiMDY4ZmMzNzhkYWE5NTJiYTdmMTYzYzRhMTE2MjhmNTVhNGRmNTIzYjNlZiIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZiIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwZmM4ZDU0YTgwZTBmYWNjNTFkNmNiOGRkMmRmNjE4NTE5MzJmOGRlZiJdLCJkYXRhIjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTNmYmU4NWVkYzkwMDAwIn0seyJhZGRyZXNzIjoiMHg2MzAxMDUxODljNzExNDY2N2E3MTc5YWE1N2YwNzY0N2E1ZjQyYjdmIiwidG9waWNzIjpbIjB4NzA4MTNjOWRjYTcwMjA0NTgwOTVkODI1MWQyYTEyNjhjZmFkZjdlYjhhMWZkMjA1NGM1NzFmY2E0NmZlYTkwNiIsIjB4ZGNiNjE2MmMxNjMyODA1M2EwNjE5ZDg3NjZiY2M2MTM2MzczMTJlY2MzNWVkN2RmY2M4ZTI4Njg2YjI2ZDk5YSIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwOWY3MjJiMmNiMzAwOTNmNzY2MjIxZmQwZDM3OTY0OTQ5ZWQ2NjkxOCJdLCJkYXRhIjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTYzNDU3ODVkOGEwMDAwIn0seyJhZGRyZXNzIjoiMHg5ZjcyMmIyY2IzMDA5M2Y3NjYyMjFmZDBkMzc5NjQ5NDllZDY2OTE4IiwidG9waWNzIjpbIjB4ZGRmMjUyYWQxYmUyYzg5YjY5YzJiMDY4ZmMzNzhkYWE5NTJiYTdmMTYzYzRhMTE2MjhmNTVhNGRmNTIzYjNlZiIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNjMwMTA1MTg5YzcxMTQ2NjdhNzE3OWFhNTdmMDc2NDdhNWY0MmI3ZiIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMCJdLCJkYXRhIjoiMHgwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDM5YTgwZTY4ZTkyMjI4MDAwIn0seyJhZGRyZXNzIjoiMHg2MzAxMDUxODljNzExNDY2N2E3MTc5YWE1N2YwNzY0N2E1ZjQyYjdmIiwidG9waWNzIjpbIjB4NDRmZjc3MDE4Njg4ZGFkNGIyNDVlOGFiOTczNThlZDU3ZWQ5MjI2OTk1MmVjZTdmZmQzMjEzNjZjZTA3ODYyMiIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNThmOCIsIjB4MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwNGQ1MDEwMDAwMDAwMSJdLCJkYXRhIjoiMHhkY2I2MTYyYzE2MzI4MDUzYTA2MTlkODc2NmJjYzYxMzYzNzMxMmVjYzM1ZWQ3ZGZjYzhlMjg2ODZiMjZkOTlhMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDBjMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTQwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwM2Y2NGRhODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAxYTAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDE0OWY3MjJiMmNiMzAwOTNmNzY2MjIxZmQwZDM3OTY0OTQ5ZWQ2NjkxODAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMTRmNTgwYjJiNDBjYTc1MzBjNmE5MmMyNDYzYWE4MTBmMGI2ZGQ3NWRkMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA0MDM0NjMzNzM0MzY2MzM1MzgzNDYzMzEzMjYyNjMzNzM2MzMzNzY2Mzc2NDM0MzU2MjYyMzAzNDMzMzgzODMyNjE2NjMxMzc2NDMwNjEzNDM5MzczMzM3MzUzNTM2NjEzMTMxMzYzMTM4NjUzOTMxMzgzMzM3MzAzNzM2NjM2MzY0MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAzYzYxMzA2MjM4MzYzOTM5MzE2MzM2MzIzMTM4NjIzMzM2NjMzMTY0MzEzOTY0MzQ2MTMyNjUzOTY1NjIzMDYzNjUzMzM2MzAzNjY1NjIzNDM4MmU2NjYxNjM3NDZmNzI3OTJlNjI3MjY5NjQ2NzY1MmU2ZTY1NjE3MjAwMDAwMDAwIn1dfX19\",\"deposit\":\"299999999999999999999999\",\"gas\":300000000000000,\"method_name\":\"transfer_in\"}}",
        "state": 1,
        "relayerLightNode": {
            "id": 4,
            "name": "NEAR Light Client on MAPO",
            "chain": "MAP",
            "chainId": "22776",
            "chainImg": "https://files.maplabs.io/bridge/map.png",
            "lightAddress": "0x4464fA3A804b8a44a0aD212eD23155a08f336B34",
            "startHeight": 2594229,
            "toChainId": "1360100178526209",
            "txHash": "0x761cad495d8785f63e45855f0fdef35c1f4baab8706ddd635410ee86bf71351c",
            "height": "97429893",
            "fromAddress": "0x9c9aa4cac48d0a5ab863ce0e1a71cd0066a42f7f",
            "timestamp": "2023-07-27 05:23:28"
        },
        "toLightNode": {
            "id": 5,
            "name": "MAPO Light Client on NEAR",
            "chain": "NEAR",
            "chainId": "1360100178526209",
            "chainImg": "https://files.maplabs.io/image/maponimn.png",
            "lightAddress": "client2.cfac.mapprotocol.near",
            "startHeight": 80104227,
            "toChainId": "22776",
            "txHash": "HexDVex7Dv3KsRv1yhdfJLNA5bUAJYSqAgQeKEY5KCLQ",
            "height": "6700000",
            "fromAddress": "841da12ac26772c3aaf91674cea7e1e40a32f4a623b48e1c861c5718012b8d2b",
            "timestamp": "2023-07-25 09:47:54"
        },
        "sourceNowHeight": 6733820,
        "relayerNowHeight": 6733820,
        "toNowHeight": 97440769,
        "relayerVerifyState": 1,
        "destinationVerifyState": 1,
        "timestamp": "2023-07-27 08:41:33",
        "sourceChainDict": {
            "id": 1,
            "chainId": 22776,
            "chainName": "MAP Relayer Chain",
            "scanUrl": "https://mapscan.io/",
            "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
            "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
            "color": null
        },
        "relayerChainDict": {
            "id": 1,
            "chainId": 22776,
            "chainName": "MAP Relayer Chain", 
            "scanUrl": "https://mapscan.io/",
            "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png",
            "mosContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
            "color": null
        },
        "destinationChainDict": {
            "id": 4,
            "chainId": 1360100178526209,
            "chainName": "Near Protocol",
            "scanUrl": "https://explorer.near.org/",
            "chainImg": "https://files.maplabs.io/image/maponimn.png",
            "mosContract": "mos.mfac.butternetwork.near",
            "color": null
        },
        "sourceMOSContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F", 
        "relayerMOSContract": "0x630105189c7114667a7179Aa57f07647a5f42B7F",
        "destinationMOSContract": "mos.mfac.butternetwork.near",
        "relayerMessengerContract": "0xf580b2b40ca7530c6a92c2463aa810f0b6dd75dd",
        "destinationMessengerContract": null
    }
}
}
```

### queryBridgeHistoryByAddress

Uri: /api/queryBridgeHistoryByAddress

Request method: GET

Return bridge history。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`chainId` - Source Chain id.

`address` - Source address.

Example:

http://host:port/api/queryBridgeHistoryByAddress?pageNo=1&pageSize=10&chainId=1&address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`id` - Id.

`sourceAddress` - Source Address.

`destinationAddress` - Destination Address.

`fromChainId` - Source Chain Id.

`toChainId` - Destination Chain Id.

`sourceHash` - Source Transactions Hash.

`sourceHeight` - Source Transactions Height.

`destinationHash` - Destination Transactions Hash.

`destinationHeight` - Destination Transactions Height.

`orderId` - Order Id.

`sourceTokenAddress` - Source Chain Token Address.

`destinationTokenAddress` - Destination Chain Token Address.

`amount` - Bridge Amount.

`state` - Bridge Status. 0: crossing 1 completed 2 relayering 3 relayer completed

`timestamp` - Bridge Transactions Time.

`sourceChain` - Source Chain Object, {id, chainId, chainName, scanUrl, chainImg}.

`destinationChain` - Destination Chain object, {id, chainId, chainName, scanUrl, chainImg}.

`sourceToken` - Source Chain Token Object.

`destinationToken` - Destination Chain Token Object.

`inAmount` - In Amount.

`completeTime` - Bridge Complete Time.


##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryBridgeHistoryByAddress?page=1&size=10&chainId=56&address=0xecef0d873d909730da0f446d3afac15ef19b45b1"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": 85510,
            "sourceAddress": "0xecef0d873d909730da0f446d3afac15ef19b45b1",
            "destinationAddress": "0xa06e0f8851438115628c5780480c53017d405e4e",
            "fromChainId": "56",
            "toChainId": "22776",
            "sourceHash": "0xfa26d7abef03d57a938b675a46e9fb2edc15003cf50c99c73eeac2de0183d166",
            "sourceHeight": 30328761,
            "destinationHash": "0xf56080d26a008eaecba32e3f99ee38016554cc0ba2a20127b24b81b33dc0d36a",
            "destinationHeight": 6736439,
            "orderId": "0x06c55c661a44abaf1089ea7285926e03d741effd09d2d932916cf99af5a3a7f0",
            "sourceTokenAddress": "0x55d398326f99059ff775485246999027b3197955",
            "destinationTokenAddress": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
            "amount": 793967907953388800000,
            "type": 0,
            "state": 1,
            "timestamp": "2023-07-27 12:21:22",
            "sourceChain": {
                "id": 3,
                "chainId": "56",
                "chainName": "BNB Chain",
                "scanUrl": "https://bscscan.com/",
                "chainImg": "https://files.maplabs.io/bridge/bsc.png"
            },
            "destinationChain": {
                "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
            },
            "sourceToken": {
                "id": 37,
                "chainId": 56,
                "address": "0x55d398326f99059fF775485246999027B3197955",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
            },
            "destinationToken": {
                "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
            },
            "inAmount": 793173940045435400000,
            "completeTime": "2023-07-27 12:23:58"
        }
        ......
    ],
        "total": 29
}
}
```


### queryBridgeInfoById

Uri: /api/queryBridgeInfoById

Request method: GET

Return to Bridge Transaction History。

##### Parameters

Parameter Type：

`id` - Id.

Example:

http://host:port/api/queryBridgeInfoById?id=1


##### Returns

`fromChain` - Source Chain Object.

`relayerChain` - Relayer Chain Object.

`toChain` - Destination Chain Object.

`tokenAddress` - Source Chain Token Address.

`tokenSymbol` - Source Chain Token Symbol.

`timestamp` - Bridge Transactions Time.

`completeTime` - Bridge Transactions Complete Time.

`amount` - Bridge Amount.

`inAmount` - In Amount.

`fee` - Bridge Transactions Fee.

`state` - Bridge Transactions Status. 0: crossing 1 completed 2 relayering 3 relayer completed

`sourceHash` - Source Chain Hash.

`relayerHash` - Relayer Chain Hash.

`toHash` - Destination Chain Hash.

`sourceAddress` - Source Address.

`toAddress` - Destination Address.

`fromTokenDecimal` - Source Chain Token Decimal.

`sourceToken` - Source Chain Token Object.

`destinationToken` - Destination Chain Token Object.

`feeToken` - Transactions Fee Token Object.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryBridgeInfoById?id=85510"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "info": {
        "fromChain": {
            "id": 3,
                "chainId": "56",
                "chainName": "BNB Chain",
                "scanUrl": "https://bscscan.com/",
                "chainImg": "https://files.maplabs.io/bridge/bsc.png"
        },
        "relayerChain": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
        },
        "toChain": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
        },
        "tokenAddress": "0x55d398326f99059fF775485246999027B3197955",
            "tokenSymbol": "USDT",
            "timestamp": "2023-07-27T12:21:22.000+00:00",
            "completeTime": "2023-07-27T12:23:58.000+00:00",
            "amount": 793.9679079533888,
            "inAmount": 793.1739400454354,
            "fee": "0.793967907953388772USDT",
            "state": 1,
            "sourceHash": "0xfa26d7abef03d57a938b675a46e9fb2edc15003cf50c99c73eeac2de0183d166",
            "relayerHash": null,
            "toHash": "0xf56080d26a008eaecba32e3f99ee38016554cc0ba2a20127b24b81b33dc0d36a",
            "sourceAddress": "0xecef0d873d909730da0f446d3afac15ef19b45b1",
            "toAddress": "0xa06e0f8851438115628c5780480c53017d405e4e",
            "fromTokenDecimal": 18,
            "sourceToken": {
            "id": 37,
                "chainId": 56,
                "address": "0x55d398326f99059fF775485246999027B3197955",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        },
        "destinationToken": {
            "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        },
        "feeToken": {
            "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        }
    }
}
}
```



### queryBridgeInfoBySourceHash

Uri: /api/queryBridgeInfoBySourceHash

Request method: GET

Return to Bridge Transaction History。

##### Parameters

Parameter Type：

`hash` - Source Chain Transaction Hash.

Example:

http://host:port/api/queryBridgeInfoBySourceHash?hash=0xfa26d7abef03d57a938b675a46e9fb2edc15003cf50c99c73eeac2de0183d166


##### Returns

`fromChain` - Source Chain Object.

`relayerChain` - Relayer Chain Object.

`toChain` - Destination Chain Object.

`tokenAddress` - Source Chain Token Address.

`tokenSymbol` - Source Chain Token Symbol.

`timestamp` - Bridge Transactions Time.

`completeTime` - Bridge Transactions Complete Time.

`amount` - Bridge Amount.

`inAmount` - In Amount.

`fee` - Bridge Transactions Fee.

`state` - Bridge Transactions Status. 0: crossing 1 completed 2 relayering 3 relayer completed

`sourceHash` - Source Chain Hash.

`relayerHash` - Relayer Chain Hash.

`toHash` - Destination Chain Hash.

`sourceAddress` - Source Address.

`toAddress` - Destination Address.

`fromTokenDecimal` - Source Chain Token Decimal.

`sourceToken` - Source Chain Token Object.

`destinationToken` - Destination Chain Token Object.

`feeToken` - Transactions Fee Token Object.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryBridgeInfoBySourceHash?hash=0xfa26d7abef03d57a938b675a46e9fb2edc15003cf50c99c73eeac2de0183d166"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "info": {
        "fromChain": {
            "id": 3,
                "chainId": "56",
                "chainName": "BNB Chain",
                "scanUrl": "https://bscscan.com/",
                "chainImg": "https://files.maplabs.io/bridge/bsc.png"
        },
        "relayerChain": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
        },
        "toChain": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
        },
        "tokenAddress": "0x55d398326f99059fF775485246999027B3197955",
            "tokenSymbol": "USDT",
            "timestamp": "2023-07-27T12:21:22.000+00:00",
            "completeTime": "2023-07-27T12:23:58.000+00:00",
            "amount": 793.9679079533888,
            "inAmount": 793.1739400454354,
            "fee": "0.793967907953388772USDT",
            "state": 1,
            "sourceHash": "0xfa26d7abef03d57a938b675a46e9fb2edc15003cf50c99c73eeac2de0183d166",
            "relayerHash": null,
            "toHash": "0xf56080d26a008eaecba32e3f99ee38016554cc0ba2a20127b24b81b33dc0d36a",
            "sourceAddress": "0xecef0d873d909730da0f446d3afac15ef19b45b1",
            "toAddress": "0xa06e0f8851438115628c5780480c53017d405e4e",
            "fromTokenDecimal": 18,
            "sourceToken": {
            "id": 37,
                "chainId": 56,
                "address": "0x55d398326f99059fF775485246999027B3197955",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        },
        "destinationToken": {
            "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        },
        "feeToken": {
            "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        }
    }
}
}
```

### queryCrossInfoByOrderId

Uri: /api/queryCrossInfoByOrderId

Request method: GET

Return to Bridge Transaction History。

##### Parameters

Parameter Type：

`orderId` - Order Id.

Example:

http://host:port/api/queryCrossInfoByOrderId?orderId=0x06c55c661a44abaf1089ea7285926e03d741effd09d2d932916cf99af5a3a7f0


##### Returns

`fromChain` - Source Chain Object.

`relayerChain` - Relayer Chain Object.

`toChain` - Destination Chain Object.

`tokenAddress` - Source Chain Token Address.

`tokenSymbol` - Source Chain Token Symbol.

`timestamp` - Bridge Transactions Time.

`completeTime` - Bridge Transactions Complete Time.

`amount` - Bridge Amount.

`inAmount` - In Amount.

`fee` - Bridge Transactions Fee.

`state` - Bridge Transactions Status. 0: crossing 1 completed 2 relayering 3 relayer completed

`sourceHash` - Source Chain Hash.

`relayerHash` - Relayer Chain Hash.

`toHash` - Destination Chain Hash.

`sourceAddress` - Source Address.

`toAddress` - Destination Address.

`fromTokenDecimal` - Source Chain Token Decimal.

`sourceToken` - Source Chain Token Object.

`destinationToken` - Destination Chain Token Object.

`feeToken` - Transactions Fee Token Object.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryCrossInfoByOrderId?orderId=0x06c55c661a44abaf1089ea7285926e03d741effd09d2d932916cf99af5a3a7f0"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "info": {
        "fromChain": {
            "id": 3,
                "chainId": "56",
                "chainName": "BNB Chain",
                "scanUrl": "https://bscscan.com/",
                "chainImg": "https://files.maplabs.io/bridge/bsc.png"
        },
        "relayerChain": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
        },
        "toChain": {
            "id": 1,
                "chainId": "22776",
                "chainName": "MAP Relayer Chain",
                "scanUrl": "https://mapscan.io/",
                "chainImg": "https://cdn.befiwalletdao.com/image/icon_local_map_checked_3gfyyv.png"
        },
        "tokenAddress": "0x55d398326f99059fF775485246999027B3197955",
            "tokenSymbol": "USDT",
            "timestamp": "2023-07-27T12:21:22.000+00:00",
            "completeTime": "2023-07-27T12:23:58.000+00:00",
            "amount": 793.9679079533888,
            "inAmount": 793.1739400454354,
            "fee": "0.793967907953388772USDT",
            "state": 1,
            "sourceHash": "0xfa26d7abef03d57a938b675a46e9fb2edc15003cf50c99c73eeac2de0183d166",
            "relayerHash": null,
            "toHash": "0xf56080d26a008eaecba32e3f99ee38016554cc0ba2a20127b24b81b33dc0d36a",
            "sourceAddress": "0xecef0d873d909730da0f446d3afac15ef19b45b1",
            "toAddress": "0xa06e0f8851438115628c5780480c53017d405e4e",
            "fromTokenDecimal": 18,
            "sourceToken": {
            "id": 37,
                "chainId": 56,
                "address": "0x55d398326f99059fF775485246999027B3197955",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        },
        "destinationToken": {
            "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        },
        "feeToken": {
            "id": 36,
                "chainId": 22776,
                "address": "0x33daba9618a75a7aff103e53afe530fbacf4a3dd",
                "name": "USDT",
                "symbol": "USDT",
                "icon": null,
                "decimal": 18,
                "isMainCurrency": 0
        }
    }
}
}
```

### queryMessageBridge

Uri: /api/queryMessageBridge

Request method: GET

Return to Message Bridge Info。

##### Parameters

Parameter Type：

`hash` - Source Chain Transactions Hash.

Example:

http://host:port/api/queryMessageBridge?hash=0x06c55c661a44abaf1089ea7285926e03d741effd09d2d932916cf99af5a3a7f0


##### Returns

`sourceHash` - Source Chain Transactions Hash.

`sourceHeight` - Source Chain Transactions Hetght.

`SourceTimestamp` - Source Chain Transactions Time.

`relayerHash` - Relayer Chain Transactions Hash. Relayer Chain transaction was not completed or failed, the field is null.

`relayerHeight` - Relayer Chain Transactions Height. Relayer Chain transaction was not completed or failed, the field is null.

`relayerTimestamp` - Relayer Chain Transactions Time. Relayer Chain transaction was not completed or failed, the field is null.

`toHash` - Destination Chain Transactions Hash. Destination Chain transaction was not completed or failed, the field is null.

`toHeight` - Destination Chain Transactions Height. Destination Chain transaction was not completed or failed, the field is null.

`toTimestamp` - Destination Chain Transactions Time. Destination Chain transaction was not completed or failed, the field is null.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "https://127.0.0.1:8403/api/queryMessageBridge?hash=0x5277ae3357d13b21413da77572311c75b01bbe1324e50540f0876604279da9ff"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "messageBridgeInfo": {
        "sourceHash": "0x5277ae3357d13b21413da77572311c75b01bbe1324e50540f0876604279da9ff",
        "sourceHeight": "37908942",
        "relayerHash": "0xa36fb96fac07b9afe63e1ee807b94831def182244a310ee10b9da64b5ba20c8e",
        "relayerHeight": "4329092",
        "relayerTimestamp": "2020-09-16T09:58:03.000+00:00",
        "toHash": "0x6d43a00d9916ee297237d77ae1f6a9f8c58478fd6bb9f50aab0430de9ea6a793",
        "toHeight": "31561158",
        "toTimestamp": "2023-07-15T00:10:40.000+00:00",
        "sourceTimestamp": "2023-07-14T02:42:18.000+00:00"
    }
}
}
```