Message EVM Chains Contract

## contract interface 

```
interface MessageEvmChainContract {
    enum MessageType {
        CALLDATA,
        MESSAGE
    }
    
    struct MessageData {
        bool relay;
        MessageType msgType;
        bytes target;
        bytes payload;
        uint256 gasLimit;
        uint256 value;
    }
    
    function initialize(address _wToken, address _lightNode) public;

 	function transferOut(
 		uint256 _toChain, 
 		bytes memory _messageData,
 		address _feeToken
  	) external payable  returns(bool);

     
    function transferIn(uint256 _chainId, bytes memory _receiptProof) external;
 
}
```

## initialize

perform initialization

### function

function initialize(address _wToken, address _lightNode)public 

### parameters

| parameter  | type    | comment                                  |
| ---------- | ------- | ---------------------------------------- |
| _wToken    | address | This is wrap contract address            |
| _lightNode | address | This is the LightClient contract address |

## transferOut

Cross-chain transfer of message

### function

function transferOut(uint256 _toChain,CallData memory _callData) external

### parameters

| parameter    | type    | comment                                              |
| ------------ | ------- | ---------------------------------------------------- |
| _toChain     | address | Target chain id to transfer out                      |
| _messageData | bytes   | This structure contains information across the chain |
| _feeToken    | address | Toke address for payment of handling charges         |



## transferIn

Perform cross-chain transfer

### function

function transferIn(uint256 _chainId, bytes memory _receiptProof) external

### parameters

| parameter     | type    | comment                       |
| ------------- | ------- | ----------------------------- |
| _chainId      | uint256 | source chain id               |
| _receiptProof | bytes   | source  chain  transfer proof |

## Data structure

ReceiptProof includes the proof and the receipt to prove.

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
```
	struct MessageData {
	    //Select whether you want the relay chain to assist in the operation
        bool relay;
        //Execute type options across chains
        MessageType msgType;
        //Target chain execution contract address
        bytes target;
        //Target chain execution calldata
        bytes payload;
        //The call method executes the gas maximum
        uint256 gasLimit;
        //The number of native tokens required by the target chain
        uint256 value;
    }
```

