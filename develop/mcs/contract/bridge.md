#Map Cross-chain Service Contract

## MCS Contract
### transferOut
Cross-chain transfer of tokens

#### function
```solidity
function transferOut(address token, address to, uint amount, uint toChainId) external
```
#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| token    | address| Token address to transfer tokens from|
| to       | uint | The destination chain receiving address of the token to be transferred out |
| amount   | uint | Amount of tokens to be transferred |
| toChainId| uint | Target chain to transfer out |

### transferOutNative
Cross-chain transfer out of the native token

#### function

```solidity
function transferOutNative(address to, uint toChainId) external
```
#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| to       | uint | The destination chain receiving address of the token to be transferred out |
| toChainId| uint | Target chain to transfer out |


### depositOut
Deposit Vault token across chains

#### function

```solidity
function depositOut(address to, uint toChainId) external
```
#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| to       | uint | The destination chain receiving address of the token to be transferred out |
| toChainId| uint | Target chain to transfer out |



### transferIn

Perform cross-chain transfer

#### function

```solidity
function transferIn(address token, address from, address payable to, uint amount, bytes32 orderId, uint fromChain, uint toChain)
external
```
#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| token    | address| Tokens transferred across chains to this chain|
| from     | address | The address to which the original chain was transferred |
| to       | address | Target chain transfer address |
| amount   | uint | Amount of tokens transferred |
| orderId  | bytes32 | The order id of the source chain |
| fromChain| uint | source chain id |
| toChain  | uint | target chain id|


### addAuthToken

Perform cross-chain transfer

#### function

```solidity
function addAuthToken(address[] memory token) external
```
#### parameters

| parameter| type   | comment |
| -------- | ------ | ------- |
| token    | address[]| List of token addresses with permissions |


