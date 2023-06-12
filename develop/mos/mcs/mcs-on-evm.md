# EVM 鏈合約

![MOS](MOS.png)

## 合約地址

[此處獲取MAPO主網和測試網MCS合約地址。](/develop/mos/mcs/README.md)


## 合約界面

```solidity
interface EvmChainContract {
    function initialize(address _wToken, address _lightNode)external;
    
    function transferOutToken(address _token, bytes memory _to, uint256 _amount, uint256 _toChain) external;
    
    function transferOutNative(bytes memory _to, uint _toChain) external;
    
    function depositToken(address _token, address _to, uint _amount) external;
    
    function depositNative(address _to) external;
    
    function transferIn(uint256 _chainId, bytes memory _receiptProof) external;
    
    function isBridgeable(address _token, uint256 _toChain) public view returns (bool);
    
    function isMintable(address _token) public view returns (bool);
 
}
```

## 用戶界面


### transferOutToken

跨鏈轉出代幣

#### 功能

function transferOutToken(address _token, bytes memory _to, uint256 _amount, uint256 _toChain) 外部

#### 參數

|參數 | 類型 | 備註                                                    |
| --------- | ------- | ------------------------------------------------------------ |
| _token    | address | 轉移代幣的代幣地址                       |
| _to       | bytes   | 轉出代幣的目的鏈接收地址  |
| _amount   | uint256 | 要轉移的代幣數量                         |
| _toChain  | uint256 | 轉出的目標鏈id                          |


### transferOutNative

原生幣跨鏈轉出

#### 功能

function	transferOutNative(bytes memory _to, uint _toChain) external

#### 儲存代幣

| 參數 | 類型 | 備註                                                    |
| --------- | ------- | ------------------------------------------------------------ |
| _to       | bytes   | 轉出代幣的目的鏈接收地址  |
| _toChain  | uint256 |目標鏈轉出                                |

### 存入本地

將代幣存入vault

#### 功能

function depositToken（address _token，address _to，uint _amount）外部

#### 參數

| 參數 | 類型 | 備註 |
| --------- | ------- | ------------------------------------- |
| _token    | address | 從中轉移代幣的代幣地址|
| _to       | address | 目標鏈轉出         |
| _amount   | uint    | 要存入的代幣數量     |

### depositeNative 存入本地

將代幣存入vault

#### function

function  depositNative(address _to) external

#### parameters

| 參數 | 類型 | 備註 |                                                     |
| --------- | ------- | ------------------------------------------------------------ |
| _to       | address | 待轉出代幣的目的鏈接收地址 |



## 信使界面

###轉入

執行跨鏈傳輸

#### 功能

function transferIn(uint256 _chainId, bytes memory _receiptProof) external

#### parameters

| 參數 | 類型 | 備註   |
| ------------- | ------- | ----------------------------- |
| _chainId      | uint256 | 起源鏈 id               |
| _receiptProof | bytes   | 起源鏈轉出證明 |


## 管理界面

###初始化

執行初始化

### 功能

function initialize(address _wToken, address _lightNode)public 

### 參數

| 參數 | 類型 | 備註                                |
| ---------- | ------- | ---------------------------------------- |
| _wToken    | address | 這是包裝合約地址          |
| _lightNode | address | 這是LightClient合約地址 |



## 數據結構

ReceiptProof包括證明和收據證明。

```
struct istanbulExtra {
    //Addresses of added committee members
    address[] validators;
    //The public key of the added committee member
    bytes[] addedPubKey;
    //G1 public key of the added committee member
    bytes[] addedG1PubKey;
    //Members removed from the previous committee are removed by bit 1 after binary encoding
    uint256 removeList;
    //The signature of the previous committee on the current header
    //Reference for specific signature and encoding rules
    bytes seal;
    //Information on current committees
    istanbulAggregatedSeal aggregatedSeal;
    //Information on the previous committee
    istanbulAggregatedSeal parentAggregatedSeal;
}
```

```
 struct TxReceiptRlp {
     //This is an indication of the type of transaction
     uint256 receiptType;
     // This is the rlp encoded data of the transaction log
     bytes receiptRlp;
 }

struct receiptProof {
    // the block header where the receipt exists
    blockHeader header;
    //the istanbulExtra struct
    istanbulExtra ist;
    // the aggregated G2 public key of the signed validators
    G2 aggPk;
    // the TxReceiptRlp struct
    TxReceiptRlp txReceiptRlp;
    // the index of the receipt in the block
    bytes keyIndex;
    // the proof to prove the existance of the above receipt
    bytes[] proof;
}
```