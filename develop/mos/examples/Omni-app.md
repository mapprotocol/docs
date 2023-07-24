````
# 如何使用Mapprotocol构建全链DAPP

## 1. 确定合同版本和项目名称

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";

contract OApp is Ownable {

}
```

## 2.  导入“@mapprotocol/mos”模块并导入“IMOSV3.sol”接口将如下所示：

Add "@mapprotocol/mos" module and importing the "IMOSV3.sol" interface would look like this:

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@mapprotocol/mos/contracts/interface/IMOSV3.sol";

contract OApp is Ownable {

}
```

合约具体细节请查看github链接 [IMOSV3](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMOSV3.sol)

## 3. 当然，让我们添加一些代码来确保我们的MOS能够正确运行

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

在上面的代码中，我们部署时需要正确的MOS合约地址。


## 4.  现在，我们来完成DAPP所需的具体逻辑

我们的需求是：在源链上发送一个数字，在目标链上执行后，结果会与这个数字累加。

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

## 5. 如何发送跨链请求

### 5.1 首先我们来完成transferOut方法中获取messageData参数的过程； 我们看一下IMOSV3的标准接口，如下面所示

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

### 5.2 实现 _getMessageData 方法。

根据上面的接口，我们得知transferOut的messageData字段是通过对struct MessageData进行编码得到的。此外，我们可以根据 MessageType 枚举来选择我们的跨链模式，它为跨链类型提供了两种选择。

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

## 6. 使用该transferOut方法发送跨链消息

### 6.1 设置 `feeToken`

通过5.1，我们注意到transferOut需要三个参数:
    * `toChain` 是我们目标的chainId，
    * `messageData` 5.2就已经可以获取了
    * `feeToken` 参数就是我们在跨链过程中要用来支付目标链执行费用的代币。我们只需要在源链上进行预付费即可. 

在这种情况下，我们将仅使用该链的原生代币进行演示，我们就用address(0)来填充feeToken。每笔跨链交易需要支付多少钱？我们可以参考下面代码中的解释
```solidity
    // @notice Gets the fee to cross to the target chain.
    // @param toChain - Target chain chainID.
    // @param feeToken - Token address that supports payment fee,if it's native, it's address(0).
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    function getMessageFee(uint256 toChain, address feeToken, uint256 gasLimit) external view returns(uint256, address);
```

### 6.2 完成一个简单而准确的_getTransferOutFee方法

在参阅IMOSV3的方法注释后, 我们了解到每次交易的费用与toChain和gasLimit相关。在 5.2 中，我们已经将每个跨链交易的gasLimit固定为500000。

```solidity
 // Similarly, this method is also intended to serve transferOut, and we will define it as internal as well.
 function _getTransferOutFee(uint256 _toChainId) internal view returns(uint256){
        (uint256 amount,) = mos.getMessageFee(_toChainId,address(0),500000);
        return amount;
 }
```

### 6.3 实现发送跨链消息的完整且最终的方法

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

## 7. 完成‘MESSAGE’跨链方法用以执行消息跨链的逻辑
发送跨链消息后，在步骤4中，我们完成了CALLDATA跨链方法的执行代码。使用该MESSAGE方法时，接收合约需要实现一个固定的方法，更多细节可以在IMpoExecutor接口中找到[IMapoExecutor](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/contracts/interface/IMapoExecutor.sol) interface for more details.

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

## 8. 给予远程合约调用权限

看起来我们已经完成了一个跨链DAPP合约，不过先别着急，还有更关键的一步。MOS 有一个巧妙的权限验证过程，要求每个跨链合约自主向源链地址授予权限。让我们完成这一步。

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

## 9. 我们已经完成了跨链DAPP合约的开发。现在，我们来看看完整的合约代码。

###  9.1 我们看一下源链上负责发送跨链请求的合约。

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

### 9.2 下面是我们目标链上负责接收和执行跨链消息的合约。

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

## 10. 合约交互的完整过程。

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
````
