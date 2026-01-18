# Cross-Chain with SDK

This document will guide developers on how to integrate our service into their applications to facilitate querying the best route from token1 on Chain A to token2 on Chain B and assembling transactions.

### Installation

To interact with SDK, we recommend installing through the [npm package](https://www.npmjs.com/package/@mapprotocol/routekits)

![Version](https://img.shields.io/badge/Version-1.0.0-blue)
![ES Version](https://img.shields.io/badge/ES-2020-yellow)
![Node Version](https://img.shields.io/badge/node-20.x-green)
```shell
npm install @mapprotocol/routekits
```

### Initialize

During initialization, two nodes are required: `router` and `common`, as well as a set of developer-private `API keys` and `secret`, which we need to obtain.

```typescript
import { Client } from '@maplabs/routekits';

const initClient = () => {
    const endpoint = {
        routes:"https://...",
        common:"https://...",
    }
    //for test
    const apikey = '3d30ba954f8f2c9...20e27';
    const secret = 'ce34985d95c985b...c4baf0fc4fe012d0a865be3349da'
    const client = new Client({routes,common},{apikey,secret})
    return client;
}

const client = initClient();
```

### Integration Steps
* To query route transaction data, the ***routes node*** needs to be set during initialization.


#### 1.Query Supported Chain Info
Use the `getChains` function to query the list of all supported chains by this service. You will receive a list of blockchains' information.

```typescript
const response = await client.getChains();
```

> Note: the chain info list may change over time as new chains are added or removed from the Router's support, please request this endpoint to get the latest supported chain info.

#### 2. Query Best Routes
Use the `getRoutes` function to query the best routes from token1 on Chain A to token2 on Chain B. These routes are sorted by totalAmountOut of token2 in descending order.

E.g. find the best swap route from 1 ETH on Ethereum to USDT on BSC with 1% slippage and test as the entrance.
```typescript
const response = await client.getRoutes({...})
```

#### 3. Assemble Transaction Data Based on Selected Route
Use the `getOrders` function to assemble transaction data based on the selected route hash from the `getRoutes` response.

E.g. assemble the transaction data based on the route hash
`0x632f11788f1dc471afe15......6339d65fe03ad27a6b4daa75dc0ba`
with the slippage of 1% and the sender address `Address1.....`
and the receiver address `Address2.....` .

```typescript
const response = await client.getOrders({...})
```

### Query
* To query detail data, the ***common node*** needs to be set during initialization.
#### 1. Transaction Detail
Use `getTransaction` function to retrieve the details of successfully sent transactions by using the transaction `hash` from the source chain.
```typescript
const response = await client.getTransaction({...})
```
#### 2. Transaction List
Use `getTransactions` function to retrieve a list of successfully sent transactions from the `wallet address` on the source chain.
```typescript
const response = await client.getTransactions({...})
```

### Finally
#### Congratulations! You have successfully used the SDK to complete the transaction and query the transaction data.