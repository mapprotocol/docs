### How to Build a DAPP Contract Using Mapprotocol:

#### 1.To determine the contract version and project name.

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";

contract OApp is Ownable {


}
```

#### 2.Importing the "@mapprotocol/mos" module and importing the "IMOSV3.sol" interface would look like this:

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";

contract OApp is Ownable {

}
```

For contract details, please look this

[IMOSV3]: https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMOSV3.sol

#### 3.Sure, let's add some code to ensure that our MOS can function correctly.

```
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

#### 4.In the above, when deploying, we need to add the correct MOS contract address. Now, let's complete the DAPP required method. Our requirement is: to send a number on the source chain, and after execution on the target chain, the result will be accumulated with this number.

```
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

#### 5. Now let's try how to complete a cross-chain transfer through MOSV3

##### 5.1 First, let's take a look at the standard interface of IMOSV3 as shown   below.

```
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
##### 5.2 Through the standard interface, we can observe that transferOut requires three parameters. toChain represents the target chain's chainId, messageData is the encoded form of the struct MessageData, and feeToken is the token you wish to use for covering the execution fee on the target chain during cross-chain operations. All that's needed is for us to complete a pre-payment on the source chain.

##### In our demonstration, we are exclusively using native tokens of the current chain. Therefore, for feeToken, we consistently input address(0). Now, let's explore the explanation below to determine the payment required for each cross-chain operation.

```
// @notice Gets the fee to cross to the target chain. 
// @param toChain - Target chain chainID.
// @param feeToken - Token address that supports payment fee,if it's native, it's address(0).
// @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.  
function getMessageFee(uint256 toChain, address feeToken, uint256 gasLimit) external view returns(uint256, address);
```

Having read the comments for the getMessageFee interface in IMOSV3, we understand that the fee for each payment is determined by tochain and gasLimit. Given that the execution complexity on the target chain is not extensive, an estimated gasLimit of 500,000 is more than sufficient. Let's proceed to create a straightforward method for accurately obtaining _getTransferOutFee.

```
 // Similarly, this method is also intended to serve transferOut, and we will define it as internal as well.
 function _getTransferOutFee(uint256 _toChainId) internal view returns(uint256){
        (uint256 amount,) = mos.getMessageFee(_toChainId,address(0),500000);
        return amount;
 }
```

##### 5.3 Continuing to review the standard interface of IMOSV3, we discern that there are two cross-chain modes, selected based on the MessageType enumeration. When opting for transferOut, it is necessary to accurately encode the struct MessageData. Let's begin by examining the construction of MessageData for cross-chain using the CALLDATA mode.

```

    // This method is intended to retrieve the messageData parameter for transferOut, so it is 
    // designed to serve internal contract functions. Therefore, we define it as internal.
    // @param _number Parameters for Cross-Chain Accumulation
    // @param _target Address for Cross-Chain Execution, with a data type of 'bytes' to ensure
    // compatibility with non-EVM chains
    function _getCalldataMessageData(
        uint256 _number,
        bytes memory _target
    )
    internal
    pure
    returns(bytes memory)
    {

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
            
        return abi.encode(messageData);
    }

    
```

5.3.1 Preparations are complete, and now we proceed to finalize the cross-chain message method in the CALLDATA mode.

```

    function sendCalldataCrossChain(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target
    )
    external
    payable
    returns(bytes32)
    {

        bytes memory messageData = _getCalldataMessageData(_number,_target);

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

5.3.2 When the source chain initiates a cross-chain request through the `sendCalldataCrossChain` method, the `crossChainAdd` on the target chain will be invoked by the MOS contract to fulfill the cross-chain accumulation requirements. Upon closer observation during the construction of the `MessageData`, we utilize the `encodeWithSelector` of the `crossChainAdd` method. This constitutes the entire process of initiating a cross-chain message from the source chain to executing it on the target chain under the CALLDATA mode.

```
    function crossChainAdd(uint256 _number) external {
    	// To prevent arbitrary access to the method from addresses other than the target 
    	// chain's MOS contract, we restrict the method to only allow calls from the MOS 
    	// address for the accumulation.
        require(msg.sender == address(mos),"do not have permission");
        cumulativeResult += _number;
    }
```

##### 5.4 Above, we've completed cross-chain functionality in the CALLDATA mode. Now, let's proceed to construct the `MessageData` for the MESSAGE mode.

```
    // This method is intended to retrieve the messageData parameter for transferOut, so it is 
    // designed to serve internal contract functions. Therefore, we define it as internal.
    // @param _number 
    // @param _target
    function _getMessageMessageData(
        uint256 _number,
        bytes memory _target
    )
    internal
    pure
    returns(bytes memory)
    {
        // For the message's cross-chain method, we only need to encode the parameters
        // required for the cross-chain operation and do not need to include the method's 
        // selector.
        bytes memory prePayload = abi.encode(_number);
        // Here, we are adding a cross-chain identifier to facilitate the organization of cross_chain messages.
        bytes memory payload = abi.encode(CROSS_CHIAN_MESSAGE,prePayload);
        // false indicates that there is no need for secondary execution on the relay.
        // IMOSV3.MessageType.MESSAGE is the option to select our cross-chain type. 
       	// IMOSV3.MessageType is an enumeration type.
        // 500000 is the maximum gasLimit estimated to be consumed by the execution of the 
        // method on the target chain.
        IMOSV3.MessageData memory messageData = 
           	IMOSV3.MessageData(false,IMOSV3.MessageType.MESSAGE,_target,payload,500000,0);
        
         return abi.encode(messageData);
    }

```

Note: The cross-chain identifier is a `bytes32`, resembling something like`keccak256("Message(bytes32,bytes)")`, and its content is arbitrary.

5.4.1 Similarly, we finalize the cross-chain message method in the MESSAGE mode.

```
    function sendMessageCrossChain(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target
    )
    external
    payable
    returns(bytes32)
    {

        bytes memory messageData = _getMessageMessageData(_number,_target);

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

5.4.2 Take note, now we move on to accomplish the execution of cross-chain using the MESSAGE mode on the target chain. Employing the MESSAGE mode for cross-chain necessitates the implementation of a specific method within the target contract. For more details, please refer to [IMapoExecutor](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMapoExecutor.sol)

```

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
        // Firstly, decipher the cross-chain type identifier to retrieve the required cross-chain data.
        (bytes32 typeTag,bytes memory payload) = abi.decode(_message,(bytes32,bytes));
        //decode number
        uint256 number = abi.decode(payload,(uint256));
        // Completed the accumulation requirement.
        cumulativeResult += number;
        // Mark _orderId as executed.
        orderList[_orderId] = true;
        
        return _message;
   }
```

5.4.3 When you utilize the MESSAGE mode for cross-chain transactions from the source chain, while constructing the `MessageData`, only encode the data required by the target chain. Consequently, on the target chain, the `mapoExecute` method will be invoked by MOS in a predetermined manner. In this method, you can incorporate your unique logic and conditions. The above explanation provides a basic demonstration of the complete process of cross-chain using the MESSAGE mode.

#### 6.In step 5, we've individually completed two distinct forms of cross-chain methods. Now, let's attempt to use a single method to achieve selectable cross-chain modes. Previously, the cross-chain transaction fees had to be provided alongside the cross-chain process. This time, we will explore an alternative approach.

```
	
    // Enable the contract to accept native tokens of this chain.
    receive() external payable {}
    
    // The "tag" serves as a selection option for the cross-chain mode. If you wish to use the 		
    // Message mode for cross-chain, input 1.
    function sendCrossChain(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target,
        uint256 _tag
    )
    external
    returns(bytes32)
    {	
    
        bytes memory messageData;
        if(_tag == 1){
             messageData = _getMessageMessageData(_number,_target);
        }else{
            messageData = _getCalldataMessageData(_number,_target);
        }

        uint256 fee = getTransferOutFee(_toChainId);

        bytes32 orderId = mos.transferOut{value:fee}(_toChainId, messageData, address(0));

        return orderId;
    }
```


Note: When using this method for cross-chain transactions, you only need to provide a small fee to the contract in advance. There's no need to include transaction fees during the actual cross-chain process.

#### 7.It seems that we have completed a cross-chain DAPP contract, but don't rush, there is one more crucial step. MOS has a clever permission verification process, which requires each cross-chain contract to autonomously grant permissions to the source chain addresses. Let's complete this step.

```
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

#### 8.We have completed the development of a cross-chain DAPP contract. Now, let's take a look at the complete contract code.

##### 8.1Let's take a look at the contract on the source chain responsible for sending cross-chain requests.

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";

contract OAppSourceSender is Ownable {

    IMOSV3 public mos;

    uint256 public cumulativeResult;

    bytes32 public constant CROSS_CHIAN_MESSAGE = keccak256("Message(bytes32,bytes)");

    receive() external payable {}

    constructor(address _mos){
        mos = IMOSV3(_mos);
    }

    function crossChainAdd(uint256 _number) external {
        require(msg.sender == address(mos),"do not have permission");
        cumulativeResult += _number;
    }

    function _getCalldataMessageData(
        uint256 _number,
        bytes memory _target
    )
    internal
    pure
    returns(bytes memory)
    {

        bytes memory payload = abi.encodeWithSelector(OAppSourceSender.crossChainAdd.selector,_number);

        IMOSV3.MessageData memory messageData =
            IMOSV3.MessageData(false,IMOSV3.MessageType.CALLDATA,_target,payload,500000,0);

        return abi.encode(messageData);
    }

    function _getMessageMessageData(
        uint256 _number,
        bytes memory _target
    )
    internal
    pure
    returns(bytes memory)
    {

        bytes memory prePayload = abi.encode(_number);
        bytes memory payload = abi.encode(CROSS_CHIAN_MESSAGE,prePayload);

        IMOSV3.MessageData memory messageData =
            IMOSV3.MessageData(false,IMOSV3.MessageType.MESSAGE,_target,payload,500000,0);

        return abi.encode(messageData);
    }

    function getTransferOutFee(uint256 _toChainId) public view returns(uint256){
        (uint256 amount,) = mos.getMessageFee(_toChainId,address(0),500000);
        return amount;
    }



    function sendCalldataCrossChain(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target
    )
    external
    payable
    returns(bytes32)
    {

        bytes memory messageData = _getCalldataMessageData(_number,_target);

        uint256 fee = getTransferOutFee(_toChainId);

        bytes32 orderId = mos.transferOut{value:fee}(_toChainId, messageData, address(0));

        return orderId;
    }

    function sendMessageCrossChain(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target
    )
    external
    payable
    returns(bytes32)
    {

        bytes memory messageData = _getMessageMessageData(_number,_target);

        uint256 fee = getTransferOutFee(_toChainId);

        bytes32 orderId = mos.transferOut{value:fee}(_toChainId, messageData, address(0));

        return orderId;
    }

    function sendCrossChain(
        uint256 _toChainId,
        uint256 _number,
        bytes memory _target,
        uint256 _tag
    )
    external
    returns(bytes32)
    {
        bytes memory messageData;
        if(_tag == 1){
             messageData = _getMessageMessageData(_number,_target);
        }else{
            messageData = _getCalldataMessageData(_number,_target);
        }

        uint256 fee = getTransferOutFee(_toChainId);

        bytes32 orderId = mos.transferOut{value:fee}(_toChainId, messageData, address(0));

        return orderId;
    }

}
```

##### 8.2 Below is the contract on our target chain responsible for receiving and executing cross-chain messages.

```
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
        (bytes32 typeTag,bytes memory payload) = abi.decode(_message,(bytes32,bytes));
        uint256 number = abi.decode(payload,(uint256));
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


##### 9.Great! Above, we have completed the implementation of a simple cross-chain DAPP contract for accumulation. Let's now go through the complete process of interacting with the contract.

```
1. Deploy our OAppSourceSender contract on the EVM source chain A to obtain the Source-contract-address.
2. Similarly, deploy our OAppTargetReceiver contract on the EVM target chain to obtain the Target-contract-address.
3. Invoke the setTrustFromAddress method on the target chain to set a security flag for Source-contract-address.
4. Call getTrustFromAddress on the target chain to verify if the setup is completed.
5. Call getCumulativeResult on the target chain to retrieve the current result.
6. Call getTransferOutFee on the source chain to determine the required fee for the target chain.
7. Initiate a cross-chain request on the source chain by using the sendCalldataCrossChain method or the sendMessageCrossChain method.
8. Alternatively, transfer a certain fee to Source-contract-address and initiate a cross-chain request by calling sendCrossChain.
9. After observing the completion of the cross-chain transaction on the cross-chain explorer, call the getTrustFromAddress method on the target chain to check if the result has been successfully accumulated.
```

