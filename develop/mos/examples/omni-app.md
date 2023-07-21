# How to Build Omni-chain Contracts Using MAPO Service Message

## 1. Determine the contract version and name

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";

contract OApp is Ownable {

}
```

## 2. Importing MOS interface "IMOSV3.sol"

Add "@mapprotocol/mos" module and importing the "IMOSV3.sol" interface would look like this:

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";

contract OApp is Ownable {

}
```

For contract details, please look [IMOSV3](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMOSV3.sol)

## 3. Ensure that MOS can function correctly

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";

contract OApp is Ownable {

    IMOSV3 public mos;
	// Add the MOS contract address as a parameter in the constructor to ensure that the MOS 
	// interface can be invoked.
    constructor(address _mos){
        mos = IMOSV3(_mos);
    }
}
```

In the above, when deploying, we need to add the correct `MOS` contract address.


## 4.  Complete the DApp required method

The requirement is: send a number on the source chain, and after execution on the target chain, the result will be accumulated with this number.

```solidity
	// This is a target chain method for accumulating numbers, which will store the accumulated 
 	// result in the mapping called "cumulativeResult."
    function crossChainAdd(uint256 _number) external {
    	// To prevent arbitrary access to the method from addresses other than the target 
    	// chain's MOS contract, we restrict the method to only allow calls from the MOS 
    	// address for the accumulation.
        require(msg.sender == address(mos),"do not have permission");
        cumulativeResult += _number;
    }
    
    // By using this method, you can retrieve the current result of cumulativeResult and verify
    // if the cross-chain accumulation has been executed.
    function getCumulativeResult() external view returns(uint256){
        return cumulativeResult;
    }
```

## 5. How to send a cross-chain request

### 5.1 Complete the process of obtaining the `messageData` parameter in the `transferOut` method

Let's take a look at the standard interface of IMOSV3 as shown below.

```solidity
	enum MessageType {
        CALLDATA,
        MESSAGE
    }
    
    // @notice This is the configuration you need across the chain.
    // @param relay - When it is true, the relay chain is required to perform a special
    // execution to continue across the chain.
    // @param msgType - Different execution patterns of messages across chains.
    // @param target - The contract address of the target chain.
    // @param payload - Cross-chain data.
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the
    // target chain.
    // @param value - Collateral value cross-chain, currently not supported, default is 0.
    struct MessageData {
        bool relay;
        MessageType msgType;
        bytes target;
        bytes payload;
        uint256 gasLimit;
        uint256 value;
    }

    // @notice Initiate cross-chain transactions. Generate cross-chain logs.
    // @param toChain - Target chain chainID.
    // @param messageData - Structure MessageData encoding.
    // @param feeToken - In what Token would you like to pay the fee. 
    function transferOut(
    	uint256 toChain, 
    	bytes memory messageData, 
    	address feeToken
    	) external payable  returns(bytes32);

```

### 5.2 Implement `_getMessageData` method

According to the above interface, we learned that the `messageData` bytes for `transferOut` are obtained by encoding the struct MessageData.
Additionally, we can choose our cross-chain mode based on the `MessageType` enumeration, which provides two options for cross-chain types.

```solidity

	// Define two constants to determine the choice of cross-chain mode.
	uint256 public constant CROSS_CHIAN_MESSAGE = 0;
	uint256 public constant CROSS_CHIAN_CALL = 1;

	// This method is intended to retrieve the messageData parameter for transferOut, so it is 
	// designed to serve internal contract functions. Therefore, we define it as internal.
	// @param _number 
	// @param _target
	    function _getMessageData(
        uint256 _number,
        bytes memory _target,
        uint256 _type
    )
    internal
    pure
    returns(bytes memory)
    {

    	// Define a bytes variable to receive the encoded value, making it easier for the 
    	// return statement.
        bytes messageByte;
        // Based on the provided type, determine the desired cross-chain method.
        if(_type == CROSS_CHIAN_MESSAGE){
        	// For the message's cross-chain method, we only need to encode the parameters
            // required for the cross-chain operation and do not need to include the method's 
            // selector.
            bytes memory payload = abi.encode(_number);
            // false indicates that there is no need for secondary execution on the relay.
        	// IMOSV3.MessageType.MESSAGE is the option to select our cross-chain type. 
            // IMOSV3.MessageType is an enumeration type.
        	// 500000 is the maximum gasLimit estimated to be consumed by the execution of the 
        	// method on the target chain.
            IMOSV3.MessageData memory messageData = 
            	IMOSV3.MessageData(false,IMOSV3.MessageType.MESSAGE,_target,payload,500000,0);
            messageByte =  abi.encode(messageData);
        }else if(_type == CROSS_CHIAN_CALL){
        	// To obtain the payload field value of the CALLDATA cross-chain method, this is 
        	// the callcode that will be executed on the target chain.
            bytes memory payload =
            	abi.encodeWithSelector(OAppSourceSender.crossChainAdd.selector,_number);
            // false indicates that there is no need for secondary execution on the relay.
        	// IMOSV3.MessageType.CALLDATA is the option to select our cross-chain type. 
            // IMOSV3.MessageType is an enumeration type.
        	// 500000 is the maximum gasLimit estimated to be consumed by the execution of the 
        	// method on the target chain.
            IMOSV3.MessageData memory messageData = 
            	IMOSV3.MessageData(false,IMOSV3.MessageType.CALLDATA,_target,payload,500000,0);
            messageByte =  abi.encode(messageData);
        }
         return messageByte;
    }

```

## 6. Use `transferOut` function to send a cross-chain message

The preparations are almost complete, and now we will attempt to use the `transferOut` function to send a cross-chain message.

### 6.1 Set `feeToken`

Through 5.1, we notice that `transferOut` requires three parameters:
    * `toChain` is the chainId of our target
    * `messageData` already obtained  from 5.2
    * `feeToken` is the token to use for paying the execution fee on the target chain during the cross-chain process. We only need to make a pre-payment on the source chain. 

In this case, we will only use the native token of this chain for demonstration purposes. 
So, we will fix `feeToken` by filling it with `address(0)`. Now, how much do we need to pay for each cross-chain transaction? 
Let's refer to the explanation in the following interface.
```solidity
    // @notice Gets the fee to cross to the target chain.
    // @param toChain - Target chain chainID.
    // @param feeToken - Token address that supports payment fee,if it's native, it's address(0).
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    function getMessageFee(uint256 toChain, address feeToken, uint256 gasLimit) external view returns(uint256, address);
```

### 6.2 Complete a simple and accurate `_getTransferOutFee` method

After reading the comments in the `IMOSV3` interface for the `getMessageFee` method, we understand that the fee for each payment is related to `toChain` and `gasLimit`. 
In 5.2, we have already fixed the `gasLimit` for each cross-chain transaction to `500000`.

```solidity
 // Similarly, this method is also intended to serve transferOut, and we will define it as internal as well.
 function _getTransferOutFee(uint256 _toChainId) internal view returns(uint256){
        (uint256 amount,) = mos.getMessageFee(_toChainId,address(0),500000);
        return amount;
 }
```

### 6.3 Implement the complete and final method for sending cross-chain messages

```solidity
  function sendCrossChainAdd(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target,
        uint256 _type
    )
    external
    returns(bytes32)
    {

        bytes memory messageData = _getMessageData(_number,_target,_type);

        uint256 fee = _getTransferOutFee(_toChainId);
		// Since we have chosen to use the native token of this chain to pay the fee, we will 
		// copy the fee value to the value parameter when calling the method.
		// The three parameters of transferOut should have been understood clearly during our 
		// construction process.
        bytes32 orderId = mos.transferOut{value:fee}(_tochainId, messageData, address(0));
		// The returned orderId is a unique identifier assigned by MOS after a successful 
		// transferOut call. It serves as a cross-chain unique identifier and will not be 
		// repeated.
        return orderId;
    }
```

## 7. Implement the execution of the message using the `MESSAGE` cross-chain method

After sending the cross-chain message, in Step 4, we completed the execution code for the `CALLDATA` cross-chain method. 
Now, we will implement the execution of the message using the `MESSAGE` cross-chain method. 
When using the `MESSAGE` method, the receiving contract needs to implement a fixed method, which can be found in the [IMapoExecutor](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMapoExecutor.sol) interface for more details.

```solidity

   // After the MESSAGE cross-chain, the method will be directly invoked by the MOS contract. 
   // Parameters such as _fromChain, _toChain, _fromAddress, and _orderId can be optionally 
   // passed and ignored if not used.
   function mapoExecute(
       uint256 _fromChain,
       uint256 _toChain,
       bytes calldata _fromAddress,
       bytes32 _orderId,
       bytes calldata _message
   ) external override returns(bytes memory newMessage){
   		// Check the _orderId to determine whether this method has been called and executed 
   		// already, and prevent duplicate calls.
        require(!orderList[_orderId],"The orderId is invalid");
        //decode number
        uint256 number = abi.decode(_message,(uint256));
        // Completed the accumulation requirement.
        cumulativeResult += number;
        // Mark _orderId as executed.
        orderList[_orderId] = true;
        
        return _message;
   }

```

## 8. Grant the remote permission

It seems that we have completed a cross-chain DAPP contract, but don't rush, there is one more crucial step. 
MOS has a clever permission verification process, which requires each cross-chain contract to autonomously grant permissions to the source chain addresses. Let's complete this step.

```solidity
	// Fill in the source chain's ID and address, and set the tag to true to indicate a 
	// willingness to trust messages coming from this source chain. With this, MOS can 
	// successfully execute the cross-chain call.
    function setTrustFromAddress(uint256 _sourceChainId, bytes memory _sourceAddress, bool _tag) external onlyOwner {
        mos.addRemoteCaller(_sourceChainId,_fromAddress,_tag);
    }
	
	// This method can be used to check if the _targetAddress on the target chain trusts the 
	// sourceAddress from the source chain.
    function getTrustFromAddress(address _targetAddress,uint256 _sourceChainId,bytes memory _sourceAddress) external view returns(bool){
        return mos.getExecutePermission(_targetAddress,_sourceChainId,_sourceAddress);
    }

```

## 9. Complete the development of a cross-chain DAPP contract

###  9.1 Contract on the source chain responsible for sending cross-chain requests

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";

contract OAppSourceSender is Ownable {

    IMOSV3 public mos;

    uint256 public cumulativeResult;

    uint256 public constant CROSS_CHIAN_MESSAGE = 0;
    uint256 public constant CROSS_CHIAN_CALL = 1;

    constructor(address _mos){
        mos = IMOSV3(_mos);
    }

    function crossChainAdd(uint256 _number) external {
        require(msg.sender == address(mos),"do not have permission");
        cumulativeResult += _number;
    }

    function _getMessageData(
        uint256 _number,
        bytes memory _target,
        uint256 _type
    )
    internal
    pure
    returns(bytes memory)
    {
        bytes memory messageByte;

        if(_type == CROSS_CHIAN_MESSAGE){
            bytes memory payload = abi.encode(_number);
            IMOSV3.MessageData memory messageData = IMOSV3.MessageData(false,IMOSV3.MessageType.MESSAGE,_target,payload,500000,0);
            messageByte =  abi.encode(messageData);
        }else if(_type == CROSS_CHIAN_CALL){
            bytes memory payload = abi.encodeWithSelector(OAppSourceSender.crossChainAdd.selector,_number);
            IMOSV3.MessageData memory messageData = IMOSV3.MessageData(false,IMOSV3.MessageType.CALLDATA,_target,payload,500000,0);
            messageByte =  abi.encode(messageData);
        }
         return messageByte;
    }

    function _getTransferOutFee(uint256 _toChainId) internal view returns(uint256){
        (uint256 amount,) = mos.getMessageFee(_toChainId,address(0),500000);
        return amount;
    }

    function sendCrossChainAdd(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target,
        uint256 _type
    )
    external
    returns(bytes32)
    {

        bytes memory messageData = _getMessageData(_number,_target,_type);

        uint256 fee = _getTransferOutFee(_toChainId);

        bytes32 orderId = mos.transferOut{value:fee}(_toChainId, messageData, address(0));

        return orderId;
    }

}
```

### 9.2 Contract on target chain responsible for receiving and executing cross-chain messages

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";
import "@mapprotocol/mos/contracts/interface/IMapoExecutor.sol";

contract OAppTargetReceiver is Ownable, IMapoExecutor{

    IMOSV3 public mos;

    uint256 public cumulativeResult;
    mapping(bytes32 => bool) orderList;

    constructor(address _mos){
        mos = IMOSV3(_mos);
    }

    function crossChainAdd(uint256 _number) external {
        require(msg.sender == address(mos),"do not have permission");
        cumulativeResult += _number;
    }

   function mapoExecute(
       uint256 _fromChain,
       uint256 _toChain,
       bytes calldata _fromAddress,
       bytes32 _orderId,
       bytes calldata _message
   ) external returns(bytes memory newMessage){
        require(!orderList[_orderId],"The orderId is invalid");
        uint256 number = abi.decode(_message,(uint256));
        cumulativeResult += number;
        orderList[_orderId] = true;
        return _message;
   }


    function getCumulativeResult() external view returns(uint256){
        return cumulativeResult;
    }


    function setTrustFromAddress(uint256 _sourceChainId, bytes memory _sourceAddress, bool _tag) external onlyOwner {
        mos.addRemoteCaller(_sourceChainId,_sourceAddress,_tag);
    }

    function getTrustFromAddress(address _targetAddress,uint256 _sourceChainId,bytes memory _sourceAddress) external view returns(bool){
        return mos.getExecutePermission(_targetAddress,_sourceChainId,_sourceAddress);
    }

}
```

## 10. The complete process of interacting with the contract.

1.  Deploy the `OAppSourceSender` contract on EVM-based Source Chain A and obtain the 
    Source-contract-address
2.  Similarly, deploy `OAppTargetReceiver` contract on EVM-based Target Chain and obtain the 
    Target-contract-address.
3.  On the Target Chain, call the setTrustFromAddress method to set a security flag for the
    Source-contract-address.
4.  Verify the completion of the setting by calling getTrustFromAddress on the Target Chain.
5.  Retrieve the current result by calling getCumulativeResult on the Target Chain.
6.  On the Source Chain, call the sendCrossChainAdd method to send a cross-chain message.
7.  After observing the cross-chain completion on the cross-chain browser, call the
    getTrustFromAddress method on the Target Chain to verify if the result has been 
    successfully accumulated.


