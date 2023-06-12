# Relay Chain Contract


![MOSRelay](MOSRelay.png)

## Contract Address

[Here to get MAPO mainnet and testnet MCS contract address.](/develop/mos/mcs/README.md)



## contract interface 

```solidity
interface RelayChainContract {
    
	function transferOutToken(address _token, bytes memory _to, uint256 _amount, uint256 _toChain) external;
    
    function transferOutNative(bytes memory _to, uint _toChain) external;
    
    function depositToken(address _token, address _to, uint _amount) external;
    
    function depositNative(address _to) external;
    
    function transferIn(uint256 _chainId, bytes memory _receiptProof) external;

    function withdraw(address _vaultToken, uint256 _vaultAmount) external;

}
```

## User Interface


### transferOutToken

Cross-chain transfer of tokens

#### function

function transferOutToken(address _token, bytes memory _to, uint256 _amount, uint256 _toChain) external

#### 參數

| 參數 | 類型 | 備註                                                        |
| :-------- | :------ | ------------------------------------------------------------ |
| _token    | address |  轉移代幣的代幣地  |
| _to       | bytes   | 轉出代幣的目的鏈接收地址 |
| _amount   | uint256 | 要轉移的代幣數量                        |
| _toChain  | uint256 | 轉出的目標鏈id                             |


### transferOutNative

跨鏈轉出原生代幣

#### 功能

function transferOutNative(bytes memory _to, uint256 _toChain) external

#### 參數
| 參數 | 類型 | 備註                                                         |
| --------- | ------- | ------------------------------------------------------------ |
| _to       | bytes   | 待轉token的目的鏈接收地址 |
| _toChain  | uint256 | 轉出的目標鏈id                             |

### depositToken

存款代幣在鏈上

#### 功能

function depositToken(address _token, address _to, uint _amount) external

#### 參數

| 參數 | 類型 | 備註                                                          |
| --------- | ------- | ------------------------------------------------------------ |
| _token    | address | 可以從中轉移代幣的地址                      |
| _to       | address | 待存入代幣的目的鏈接收地址 |
| _amount   | uint    | 要存入的代幣數量                             |

### depositNative

存款原生代幣在鏈上

#### 功能

功能 depositNative(address _to) external

#### 參數

| 參數 | 類型 | 備註                                                         |
| :-------- | :------ | ------------------------------------------------------------ |
| _to       | address | 待轉出代幣的目的鏈接收地址 |



### 取出

取出質押代幣及收入

#### 功能

功能 withdraw(address _vaultToken, uint256 _vaultAmount) external

#### 參數

| 參數 | 類型 | 備註                       |
| ------------ | ------- | ------------------------- |
| _vaultToken  | address | valut 代幣地址     |
| _vaultAmount | uint256 | 代幣取出數量 |

## 信使界面



### transferIn

執行跨鏈傳輸

#### 功能

功能 transferIn(uint256 _chainId, bytes memory _receiptProof) external

#### 參數

| 參數 | 類型 | 備註                      |
| ------------- | ------- | ----------------------------- |
| _chainId      | uint256 | 起源鏈 id               |
| _receiptProof | bytes   | 起源鏈轉出證明 |
### depositIn

執行跨鏈傳輸

#### 功能

功能 depositIn(uint256 _chainId, bytes memory _receiptProof) external

#### 參數

| 參數 | 類型 | 備註                    |
| ------------- | ------- | --------------------------- |
| _chainId      | uint256 | 起源鏈 id               |
| _receiptProof | bytes   | 起源鏈轉出證明 |


## 數據結構

ReceiptProof包括證明和收據證明。

```

struct receiptProof {

    // the block header where the receipt exists
    blockHeader header;
    // This is the structure that contains the transaction log
    TxReceipt txReceipt;
    // the index of the receipt in the block
    bytes keyIndex;
    // the proof to prove the existance of the above receipt
    bytes[] proof;

}

```