
## Scan API Reference

List of APIs

- [queryMapChainData](#queryMapChainData)

### queryMapChainData

Uri: /scan/queryMapChainData

Request method: GET

Return map chain information, you can query the circulation, pledge amount, address number, transaction number, latest height and latest session number

##### Parameters

Parameter Type：

`typeStr` - Query type, including supply, staking, address, transfer, block, epoch.

Example:

http://host:port/scan/queryMapChainData?typeStr=supply,staking,address,transfer,block,epoch

##### Returns

`supply` - supply.

`staking` - staking.

`address` - address amount.

`transfer` - transfer amount.

`block` - Latest block height.

`epoch` - The latest epoch.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryMapChainData?typeStr=supply,staking,address,transfer,block,epoch"
    
// Result
{
    "code": 200,
    "message": "success",
    "data": {
        "supply": "2004823827334528978297557524",
        "staking": "4570000000000000000002848",
        "address": "106",
        "transfer": "75078",
        "block": "319347",
        "epoch": "320"
}
}
```