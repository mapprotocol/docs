# Message Cross-chain

Send cross-chain messages to call contracts or sync data across chains.

## Overview

MOS Message enables:

- Call contracts on chain B from chain A
- Sync data changes from chain A to chain B

MOS uses MAP Protocol Light Client to verify cross-chain message transactions, ensuring authenticity and on-chain traceability.

## Prerequisites

- Application must be on a MAP Protocol supported chain
- Cross-chain executable contract must authorize the MOS contract
- Both source and destination chains must have MOS Message contracts deployed

## How It Works

### On Source Chain

1. DApp prepares cross-chain message and target chain callData
2. DApp calls MOS `transferOut` method, paying cross-chain gas fee
3. MOS emits cross-chain message log

### On MAP Relay Chain

1. Messenger detects message log on source chain
2. Messenger builds proof data and calls `transferIn` on MOS Relay
3. MOS Relay verifies via Light Client
4. If MAP Relay Chain is destination, executes call; otherwise emits new event

### On Destination Chain

1. Messenger detects message log on MAP Relay Chain
2. Messenger builds proof and calls `transferIn` on destination MOS
3. MOS verifies message via Light Client
4. Executes cross-chain contract call

## Message Types

```solidity
enum MessageType {
    CALLDATA,  // Execute calldata directly
    MESSAGE    // Call mapoExecute interface
}
```

### CALLDATA Mode

Target contract method is called directly with encoded calldata.

### MESSAGE Mode

Target contract must implement `IMapoExecutor` interface:

```solidity
interface IMapoExecutor {
    function mapoExecute(
        uint256 _fromChain,
        uint256 _toChain,
        bytes calldata _fromAddress,
        bytes32 _orderId,
        bytes calldata _message
    ) external returns (bytes memory);
}
```

## Contract Interface

```solidity
interface IMOSV3 {
    struct MessageData {
        bool relay;           // Use relay chain for execution
        MessageType msgType;  // CALLDATA or MESSAGE
        bytes target;         // Target contract address
        bytes payload;        // Cross-chain data
        uint256 gasLimit;     // Gas limit on target chain
        uint256 value;        // Native token amount (currently 0)
    }
    
    // Send cross-chain message
    function transferOut(
        uint256 toChain,
        bytes memory messageData,
        address feeToken
    ) external payable returns (bytes32);
    
    // Get cross-chain fee
    function getMessageFee(
        uint256 toChain,
        address feeToken,
        uint256 gasLimit
    ) external view returns (uint256, address);
    
    // Set trusted source address
    function addRemoteCaller(
        uint256 _fromChain,
        bytes memory _fromAddress,
        bool _tag
    ) external;
}
```

## Usage Example

### Send Cross-chain Message (CALLDATA Mode)

```solidity
contract MyDApp {
    IMOSV3 public mos;
    
    function sendCrossChain(
        uint256 _toChainId,
        bytes memory _target,
        uint256 _number
    ) external payable {
        // Encode target method call
        bytes memory payload = abi.encodeWithSelector(
            ITarget.crossChainAdd.selector,
            _number
        );
        
        // Build MessageData
        IMOSV3.MessageData memory messageData = IMOSV3.MessageData({
            relay: false,
            msgType: IMOSV3.MessageType.CALLDATA,
            target: _target,
            payload: payload,
            gasLimit: 500000,
            value: 0
        });
        
        // Get fee
        (uint256 fee,) = mos.getMessageFee(_toChainId, address(0), 500000);
        
        // Send cross-chain message
        mos.transferOut{value: fee}(
            _toChainId,
            abi.encode(messageData),
            address(0)
        );
    }
}
```

### Receive Cross-chain Message (Target Contract)

```solidity
contract TargetContract {
    IMOSV3 public mos;
    uint256 public cumulativeResult;
    
    // For CALLDATA mode - called directly by MOS
    function crossChainAdd(uint256 _number) external {
        require(msg.sender == address(mos), "only MOS");
        cumulativeResult += _number;
    }
    
    // For MESSAGE mode - must implement IMapoExecutor
    function mapoExecute(
        uint256 _fromChain,
        uint256 _toChain,
        bytes calldata _fromAddress,
        bytes32 _orderId,
        bytes calldata _message
    ) external returns (bytes memory) {
        require(msg.sender == address(mos), "only MOS");
        
        uint256 number = abi.decode(_message, (uint256));
        cumulativeResult += number;
        
        return _message;
    }
    
    // Set trusted source contract
    function setTrustedSource(
        uint256 _chainId,
        bytes memory _sourceAddress
    ) external onlyOwner {
        mos.addRemoteCaller(_chainId, _sourceAddress, true);
    }
}
```

## Security

Before receiving cross-chain messages, the target contract must:

1. Call `addRemoteCaller` to trust the source chain contract address
2. Verify `msg.sender == mos` in receiving functions

## Complete Tutorial

See [Build OmniApp](./omni-app.md) for a complete step-by-step tutorial.
