
## Scan API URL

https://mainnet-api.mapscan.io/scan/api_method

## Scan API Reference

List of APIs

- [queryMapChainData](#queryMapChainData)
- [queryCommitteeList](#queryCommitteeList)
- [queryCommitteeInfoByAddress](#queryCommitteeInfoByAddress)
- [queryValidatorVoteList](#queryValidatorVoteList)
- [queryRewardList](#queryRewardList)
- [queryVoterRewardList](#queryVoterRewardList)
- [queryVoterList](#queryVoterList)
- [queryVoterInfo](#queryVoterInfo)
- [queryPocTransactionList](#queryPocTransactionList)
- [queryValidatorData](#queryValidatorData)


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


### queryCommitteeInfoByAddress

Uri: /scan/queryCommitteeInfoByAddress

Request method: GET

Get committee details by address.

##### Parameters

Parameter Type：

`address` - Validator address.

Example:

http://host:port/scan/queryCommitteeInfoByAddress?address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`voteReward` - Vote reward.

`signAddress` - sign address.

`lockedAmount` - locked amount.

`accountAddress` - account address.

`votePercent` - vote percent.

`version` - epoch

`upTime` - 	Activity

`isValidator` - Is it selected

`votedAmount` - vote amount

`name` - validator name


##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryCommitteeInfoByAddress?address=0x30b3953cf4c60e648202e9808d81f750ebe0f694"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "committeeBasicInfo": {
        "voteReward": 100000000000000000,
            "signAddress": "0x096bf1097f3af73b716eab545001d97b2cf1fb20",
            "lockedAmount": "1172706449891990589449845",
            "accountAddress": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "votePercent": 25.1653,
            "version": 323,
            "url": "",
            "createdAt": "2022-09-27T08:10:10.012+0000",
            "upTime": "100",
            "rewardAddress": null,
            "pk2": "0x1d330a79f1374d37c618bcb34edc38f99935a9f44d3885672232495e22fce1512b742d040ff3e9a996b79406cc4f18fc6c9b4a28ee7c3e88590406259f404531",
            "blockNumber": 322316,
            "pk1": "0x1688e878a03c11987010a4ea93458608cfe6ddc93e48ef163eecbdc1a035b2462fe0724e3d47e1209e46b83023926d5b450ddbda81dd99bbfbb06c1abb3a75ce301fc331df3f4222c67afe5969556409fad03fd0acf28009bb3b1a8823fcbdfa0800d60ce047cb200f8e6f5c8aa5d80a436148705d237fbd3566a01b1598fec9",
            "name": "validator 003",
            "id": "6332afe23169e492cfebd863",
            "votedAmount": "1172706449891990589449845",
            "isValidator": 1,
            "updatedAt": "2022-09-27T08:36:30.034+0000"
    }
}
}
```

### queryCommitteeList

Uri: /scan/queryCommitteeList

Request method: GET

Return a list of validators。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`name` - Validator name.

`address` - Validator address.

Example:

http://host:port/scan/queryCommitteeList?pageNo=1&pageSize=10&name=test&address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`voteReward` - Vote reward.

`signAddress` - sign address.

`lockedAmount` - locked amount.

`accountAddress` - account address.

`votePercent` - vote percent.

`version` - epoch

`upTime` - 	Activity

`isValidator` - Is it selected

`votedAmount` - vote amount

`name` - validator name


##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryCommitteeList?page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "voteReward": 100000000000000000,
            "signAddress": "0x096bf1097f3af73b716eab545001d97b2cf1fb20",
            "lockedAmount": "1172706449891990589449845",
            "accountAddress": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "votePercent": 25.1653,
            "version": 323,
            "url": "",
            "createdAt": "2022-09-27T08:10:10.012+0000",
            "upTime": "100",
            "rewardAddress": null,
            "pk2": "0x1d330a79f1374d37c618bcb34edc38f99935a9f44d3885672232495e22fce1512b742d040ff3e9a996b79406cc4f18fc6c9b4a28ee7c3e88590406259f404531",
            "blockNumber": 322203,
            "pk1": "0x1688e878a03c11987010a4ea93458608cfe6ddc93e48ef163eecbdc1a035b2462fe0724e3d47e1209e46b83023926d5b450ddbda81dd99bbfbb06c1abb3a75ce301fc331df3f4222c67afe5969556409fad03fd0acf28009bb3b1a8823fcbdfa0800d60ce047cb200f8e6f5c8aa5d80a436148705d237fbd3566a01b1598fec9",
            "name": "validator 003",
            "id": "6332afe23169e492cfebd863",
            "votedAmount": "1172706449891990589449845",
            "isValidator": 1,
            "updatedAt": "2022-09-27T08:27:06.035+0000"
        },
        {
            "voteReward": 100000000000000000,
            "signAddress": "0x2f3079a1c1c0995a1c9803853d1b8444cce0aa9f",
            "lockedAmount": "1172475757174271386767358",
            "accountAddress": "0x7806cc666a811f31e8c6f8acb58fbbfc22c8ee7e",
            "votePercent": 25.1604,
            "version": 323,
            "url": "",
            "createdAt": "2022-09-27T08:10:10.012+0000",
            "upTime": "100",
            "rewardAddress": null,
            "pk2": "0x2801781ffcf2371c911090b1dfe626a7b4e745810f30d545e45b965674bee6b323ef4f51b21bd4d141e484ff8f9d5becddc4ffe0d432a80d59b982aab1f9e575",
            "blockNumber": 322203,
            "pk1": "0x180abe920497971aefa3f9f21eddada7feb582d21d8363162478715a5df5dc1419c5a85959077abb094d83d756c8336af7d20cd34bb080250938006c1db1a52320e26260d769e1adcfe767a997021bad58b575cc83875afcbc005d9dc0447e0e01185280e2d574520cc089c90cae8fd8ad199463ed72aceaf383761aeceb20af",
            "name": "validator 002",
            "id": "6332afe23169e492cfebd862",
            "votedAmount": "1172475757174271386767358",
            "isValidator": 1,
            "updatedAt": "2022-09-27T08:27:06.030+0000"
        },
        {
            "voteReward": 100000000000000000,
            "signAddress": "0xec3e016916ba9f10762e33e03e8556409d096fb4",
            "lockedAmount": "1166122574164811999403879",
            "accountAddress": "0x12a281134c9838f8be7360621933f73d89d7a506",
            "votePercent": 25.024,
            "version": 323,
            "url": "",
            "createdAt": "2022-09-27T08:10:10.011+0000",
            "upTime": "100",
            "rewardAddress": null,
            "pk2": "0x01370ecd3f4871a718079cb799ed57597b6087eb09811fae7635f541a0b14c571b327c6f9d07f6f2b666e341fa7cb3531ee510da50fedc567739a7040a1dc696",
            "blockNumber": 322203,
            "pk1": "0x221e1cf30fdc5191e07f05ae396b37bce218abbbf4d17da32915f6ac68fb8b120afe1fdaa4999792cb33529acebe3d67cfec3586226a525dd5a6bca30d8d969b1ecb63d2f7e138cdb60bb12710a9e80f908eb6f98bdd93b8e36044d5edbadd8d1e0029286c2ba1de55393f5a262d4bc40252551b6c87a9e84ea096885a5179dc",
            "name": "validator 000",
            "id": "6332afe23169e492cfebd860",
            "votedAmount": "1166122574164811999403879",
            "isValidator": 1,
            "updatedAt": "2022-09-27T08:27:06.033+0000"
        },
        {
            "voteReward": 100000000000000000,
            "signAddress": "0x6f08db5ba52d896f2472eb49580ac6d8d0351a66",
            "lockedAmount": "1148695218768926024382215",
            "accountAddress": "0x69c53b8875fc1d9b425cb9003d0ae12a583776a0",
            "votePercent": 24.6501,
            "version": 323,
            "url": "",
            "createdAt": "2022-09-27T08:10:10.012+0000",
            "upTime": "100",
            "rewardAddress": null,
            "pk2": "0x2dc393cb4e1d6bb5e26c4fef0ccdde874535af1da42f64b34525a399dc1bbe621291bd0437dbb1f7ea7737ad515546b8f6b696ea0b9f6f49d5f6c039259ae778",
            "blockNumber": 322203,
            "pk1": "0x10a319963e7052692d751404c12b24f07a0eb1d57e599350c1788d4b82fff0c121f9ecae72aef0920080b6cbbe339a5cb1729cc3879f158c4648c6239e8d0639094abee79568feafe3ff5a4e69920298b0a862b1b5bc681fe8b9c2ac2d80ab8c2785d122d865a2581ba8a6f46316ab3edc8575e93c816ca3e9e066437bb71818",
            "name": "validator 001",
            "id": "6332afe23169e492cfebd861",
            "votedAmount": "1148695218768926024382215",
            "isValidator": 1,
            "updatedAt": "2022-09-27T08:27:06.030+0000"
        }
    ],
        "total": 4
}
}
```


### queryValidatorVoteList

Uri: /scan/queryValidatorVoteList

Request method: GET

Back to voting list。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`address` - Validator address.

Example:

http://host:port/scan/queryValidatorVoteList?pageNo=1&pageSize=10&address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`id` - Validator address.

`account` - voter address.

`value` - vote amount.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryValidatorVoteList?address=0x30b3953cf4c60e648202e9808d81f750ebe0f694&page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "number": 0,
            "validator": null,
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 1205132.6509147724,
            "version": 0,
            "type": 0,
            "createdAt": "2022-09-27T14:45:23.441+0000",
            "updatedAt": "2022-09-27T14:45:23.441+0000"
        }
    ],
        "total": 1
}
}
```



### queryRewardList

Uri: /scan/queryRewardList

Request method: GET

Back to reward list。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`address` - Validator address.

Example:

http://host:port/scan/queryRewardList?pageNo=1&pageSize=10&address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`epoch` - epoch.

`address` - validator address.

`reward` - validator reward.

`rewardStr` - validator reward (wei)

`voterReward` - voter reward

`voterRewardStr` - voter reward (wei)

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryRewardList?address=0x30b3953cf4c60e648202e9808d81f750ebe0f694&page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": "6332fe023169e492cfebf473",
            "number": 326000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 878.7199525198537,
            "rewardStr": "878719952519853675281",
            "voterReward": 7908.479572678683,
            "voterRewardStr": "7908479572678683077531",
            "createdAt": "2022-09-27T13:43:30.133+0000",
            "updatedAt": "2022-09-27T13:43:30.192+0000"
        },
        {
            "id": "6332ea7a3169e492cfebed5a",
            "number": 325000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 890.8514506167194,
            "rewardStr": "890851450616719371241",
            "voterReward": 8017.663055550474,
            "voterRewardStr": "8017663055550474341174",
            "createdAt": "2022-09-27T12:20:10.099+0000",
            "updatedAt": "2022-09-27T12:20:10.142+0000"
        },
        {
            "id": "6332d6f23169e492cfebe670",
            "number": 324000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 906.4961400154849,
            "rewardStr": "906496140015484919990",
            "voterReward": 8158.465260139365,
            "voterRewardStr": "8158465260139364279919",
            "createdAt": "2022-09-27T10:56:50.103+0000",
            "updatedAt": "2022-09-27T10:56:50.172+0000"
        },
        {
            "id": "6332c36a3169e492cfebdf76",
            "number": 323000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 926.8436816014407,
            "rewardStr": "926843681601440678876",
            "voterReward": 8341.593134412966,
            "voterRewardStr": "8341593134412966109892",
            "createdAt": "2022-09-27T09:33:30.184+0000",
            "updatedAt": "2022-09-27T09:33:30.210+0000"
        },
        {
            "id": "6332afe23169e492cfebd86e",
            "number": 322000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 953.6024162988333,
            "rewardStr": "953602416298833279514",
            "voterReward": 8582.421746689499,
            "voterRewardStr": "8582421746689499515629",
            "createdAt": "2022-09-27T08:10:10.112+0000",
            "updatedAt": "2022-09-27T08:10:10.167+0000"
        },
        {
            "id": "63329c5e3169e492cfebce7d",
            "number": 321000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 897.8366941616476,
            "rewardStr": "897836694161647644150",
            "voterReward": 8080.530247454829,
            "voterRewardStr": "8080530247454828797353",
            "createdAt": "2022-09-27T06:46:54.103+0000",
            "updatedAt": "2022-09-27T06:46:54.157+0000"
        },
        {
            "id": "633288d23169e492cfebc43b",
            "number": 320000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 835.1983652871161,
            "rewardStr": "835198365287116049777",
            "voterReward": 7516.785287584044,
            "voterRewardStr": "7516785287584044448000",
            "createdAt": "2022-09-27T05:23:30.148+0000",
            "updatedAt": "2022-09-27T05:23:30.213+0000"
        },
        {
            "id": "6332754a3169e492cfebbabc",
            "number": 319000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 835.6661752751392,
            "rewardStr": "835666175275139160797",
            "voterReward": 7520.995577476252,
            "voterRewardStr": "7520995577476252447178",
            "createdAt": "2022-09-27T04:00:10.115+0000",
            "updatedAt": "2022-09-27T04:00:10.177+0000"
        },
        {
            "id": "633261c23169e492cfebb21f",
            "number": 318000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 836.2518143951565,
            "rewardStr": "836251814395156555759",
            "voterReward": 7526.266329556409,
            "voterRewardStr": "7526266329556409001833",
            "createdAt": "2022-09-27T02:36:50.088+0000",
            "updatedAt": "2022-09-27T02:36:50.148+0000"
        },
        {
            "id": "63324e3a3169e492cfebaae7",
            "number": 317000,
            "epoch": "6",
            "address": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 836.985236520463,
            "rewardStr": "836985236520463030948",
            "voterReward": 7532.867128684167,
            "voterRewardStr": "7532867128684167278536",
            "createdAt": "2022-09-27T01:13:30.108+0000",
            "updatedAt": "2022-09-27T01:13:30.172+0000"
        }
    ],
        "total": 26
}
}
```

### queryVoterRewardList

Uri: /scan/queryVoterRewardList

Request method: GET

Back to voter reward list。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`address` - voter address.

Example:

http://host:port/scan/queryVoterRewardList?pageNo=1&pageSize=10&address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`version` - epoch.

`validator` - validator address.

`voter` - voter address.

`reward` - reward

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryVoterRewardList?address=0x30b3953cf4c60e648202e9808d81f750ebe0f694&page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": "6333118a3169e492cfebfb5f",
            "version": 327,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 7823.254968287541,
            "createdAt": "2022-09-27T15:06:50.069+0000",
            "updatedAt": "2022-09-27T15:06:50.069+0000"
        },
        {
            "id": "6332fe023169e492cfebf46c",
            "version": 326,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 7908.479572678683,
            "createdAt": "2022-09-27T13:43:30.074+0000",
            "updatedAt": "2022-09-27T13:43:30.074+0000"
        },
        {
            "id": "6332ea7a3169e492cfebed51",
            "version": 325,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 8017.663055550474,
            "createdAt": "2022-09-27T12:20:10.055+0000",
            "updatedAt": "2022-09-27T12:20:10.055+0000"
        },
        {
            "id": "6332d6f23169e492cfebe668",
            "version": 324,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 8158.465260139365,
            "createdAt": "2022-09-27T10:56:50.061+0000",
            "updatedAt": "2022-09-27T10:56:50.061+0000"
        },
        {
            "id": "6332c36a3169e492cfebdf6c",
            "version": 323,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 8341.593134412966,
            "createdAt": "2022-09-27T09:33:30.093+0000",
            "updatedAt": "2022-09-27T09:33:30.093+0000"
        },
        {
            "id": "6332afe23169e492cfebd868",
            "version": 322,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 8582.421746689499,
            "createdAt": "2022-09-27T08:10:10.057+0000",
            "updatedAt": "2022-09-27T08:10:10.057+0000"
        },
        {
            "id": "63329c5e3169e492cfebce75",
            "version": 321,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 8080.530247454829,
            "createdAt": "2022-09-27T06:46:54.062+0000",
            "updatedAt": "2022-09-27T06:46:54.062+0000"
        },
        {
            "id": "633288d23169e492cfebc434",
            "version": 320,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 7516.785287584044,
            "createdAt": "2022-09-27T05:23:30.069+0000",
            "updatedAt": "2022-09-27T05:23:30.069+0000"
        },
        {
            "id": "6332754a3169e492cfebbab5",
            "version": 319,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 7520.995577476252,
            "createdAt": "2022-09-27T04:00:10.062+0000",
            "updatedAt": "2022-09-27T04:00:10.062+0000"
        },
        {
            "id": "633261c23169e492cfebb216",
            "version": 318,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "voter": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "reward": 7526.266329556409,
            "createdAt": "2022-09-27T02:36:50.062+0000",
            "updatedAt": "2022-09-27T02:36:50.062+0000"
        }
    ],
        "total": 27
}
}
```



### queryVoterList

Uri: /scan/queryVoterList

Request method: GET

Back to voter list。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`address` - voter address.

Example:

http://host:port/scan/queryVoterList?pageNo=1&pageSize=10&address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`validator` - validator address.

`account` - voter address.

`value` - voter amount.

`version` - epoch

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryVoterList?address=0x30b3953cf4c60e648202e9808d81f750ebe0f694&page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": "6333118a3169e492cfebfb62",
            "number": 327000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 7823.254968287541,
            "version": 328,
            "type": 3,
            "createdAt": "2022-09-27T15:06:50.081+0000",
            "updatedAt": "2022-09-27T15:06:50.081+0000"
        },
        {
            "id": "6332fe023169e492cfebf470",
            "number": 326000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 7908.479572678683,
            "version": 327,
            "type": 3,
            "createdAt": "2022-09-27T13:43:30.086+0000",
            "updatedAt": "2022-09-27T13:43:30.086+0000"
        },
        {
            "id": "6332ea7a3169e492cfebed54",
            "number": 325000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 8017.663055550474,
            "version": 326,
            "type": 3,
            "createdAt": "2022-09-27T12:20:10.064+0000",
            "updatedAt": "2022-09-27T12:20:10.064+0000"
        },
        {
            "id": "6332d6f23169e492cfebe66f",
            "number": 324000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 8158.465260139365,
            "version": 325,
            "type": 3,
            "createdAt": "2022-09-27T10:56:50.080+0000",
            "updatedAt": "2022-09-27T10:56:50.080+0000"
        },
        {
            "id": "6332c36a3169e492cfebdf73",
            "number": 323000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 8341.593134412966,
            "version": 324,
            "type": 3,
            "createdAt": "2022-09-27T09:33:30.130+0000",
            "updatedAt": "2022-09-27T09:33:30.130+0000"
        },
        {
            "id": "6332afe23169e492cfebd86c",
            "number": 322000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 8582.421746689499,
            "version": 323,
            "type": 3,
            "createdAt": "2022-09-27T08:10:10.068+0000",
            "updatedAt": "2022-09-27T08:10:10.068+0000"
        },
        {
            "id": "63329c5e3169e492cfebce7c",
            "number": 321000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 8080.530247454829,
            "version": 322,
            "type": 3,
            "createdAt": "2022-09-27T06:46:54.082+0000",
            "updatedAt": "2022-09-27T06:46:54.082+0000"
        },
        {
            "id": "633288d23169e492cfebc436",
            "number": 320000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 7516.785287584044,
            "version": 321,
            "type": 3,
            "createdAt": "2022-09-27T05:23:30.085+0000",
            "updatedAt": "2022-09-27T05:23:30.085+0000"
        },
        {
            "id": "6332754a3169e492cfebbab8",
            "number": 319000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 7520.995577476252,
            "version": 320,
            "type": 3,
            "createdAt": "2022-09-27T04:00:10.078+0000",
            "updatedAt": "2022-09-27T04:00:10.078+0000"
        },
        {
            "id": "633261c23169e492cfebb21d",
            "number": 318000,
            "validator": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "account": "0x30b3953cf4c60e648202e9808d81f750ebe0f694",
            "value": 7526.266329556409,
            "version": 319,
            "type": 3,
            "createdAt": "2022-09-27T02:36:50.076+0000",
            "updatedAt": "2022-09-27T02:36:50.076+0000"
        }
    ],
        "total": 28
}
}
```

### queryVoterInfo

Uri: /scan/queryVoterInfo

Request method: GET

Back to voter list。

##### Parameters

Parameter Type：

`address` - voter address.

Example:

http://host:port/scan/queryVoterInfo?address=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`voteAmount` - vote amount.

`voteValidatorAmount` - voted validator amount.

`rewardAmount` - reward amount.

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryVoterInfo?address=0x30b3953cf4c60e648202e9808d81f750ebe0f694"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "voteAmount": "1212955.90588306",
        "voteValidatorAmount": "1",
        "rewardAmount": "212955.90588305963"
}
}
```


### queryPocTransactionList

Uri: /scan/queryPocTransactionList

Request method: GET

Back to validator transfer list。

##### Parameters

Parameter Type：

`pageNo` - Integer of page number, default is 1.

`pageSize` - Integer of page size, default is 10.

`from` - validator address.

Example:

http://host:port/scan/queryPocTransactionList?pageNo=1&pageSize=10&from=0xAfdE7710588ED92DE6924d5fbE3146005d9B7b1A


##### Returns

`method` - transfer method name.

`eventMethod` - event topic0.

`txHash` - transfer hash.

`contract` - contract address

`from` - from address

`to` - to address

`value` - value

`input` - input

`status` - status 0 fail 1 success

##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryPocTransactionList?from=0x30b3953cf4c60e648202e9808d81f750ebe0f694&page=1&size=10"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "list": [
        {
            "id": "6310d138941ad5d21ee41b39",
            "method": "vote",
            "eventMethod": "0xc042b2b90064506b5645f918161286a366d16838e9f9234a27d03a4f447257d8",
            "number": 1145032,
            "contract": "0x000000000000000000000000000000000000d013",
            "txHash": "0xb8debe690041d1e4522d9c61e660574522c795edea20db21aa25e9a9c0fca439",
            "from": "0xa96dc832a937cbefb57eef0761ab7b6b41852f98",
            "to": "0xa96dc832a937cbefb57eef0761ab7b6b41852f98",
            "value": "1000000000000000000000000",
            "input": "0x580d747a000000000000000000000000a96dc832a937cbefb57eef0761ab7b6b41852f9800000000000000000000000000000000000000000000d3c21bcecceda10000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000a073bce4df9a6351fd978e884da943b42451d16a",
            "status": 1,
            "timestamp": "2022-09-01T15:35:19.000+0000",
            "createdAt": "2022-09-01T15:35:20.086+0000",
            "updatedAt": "2022-09-01T15:35:20.086+0000"
        }
    ],
        "total": 1
}
}
```

### queryValidatorData

Uri: /scan/queryValidatorData

Request method: GET

Back to validator data。

##### Parameters

Parameter Type：

none

Example:

http://host:port/scan/queryValidatorData


##### Returns

`epoch` - epoch.

`count` - validator count.

`totalVotes` - total votes (wei)


##### Example
```js
// Request
curl -X GET -H  "Accept:application/json;charset=UTF-8" -H  "Content-Type:application/x-www-form-urlencoded" "http://18.139.224.21:9002/scan/queryValidatorData"

// Result
{
    "code": 200,
    "message": "success",
    "data": {
    "epoch": 328,
        "count": 4,
        "totalVotes": 4810000000000000000004048
}
}
```