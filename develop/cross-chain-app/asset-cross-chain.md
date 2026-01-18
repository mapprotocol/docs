# Asset Cross-chain

Transfer tokens between different blockchains using MOS.

## How It Works

![MOS Flow](../cross-chain-app/images/mcs_final.png)

### Transfer Out (Source Chain)

1. User authorizes asset deduction
2. User calls MOS contract specifying target chainId and amount
3. Contract maps token to target chain, calculates fees
4. Generates order info and emits `transferOut` event

### Relay (MAP Relay Chain)

1. Messenger detects `transferOut` event on source chain
2. Messenger builds Merkle proof
3. Messenger calls `transferIn` on MAP Relay Chain
4. MOS Relay verifies proof via Light Client
5. If not final destination, emits new `transferOut` event

### Transfer In (Destination Chain)

1. Messenger detects event on MAP Relay Chain
2. Messenger builds Merkle proof
3. Messenger calls `transferIn` on destination chain
4. MOS verifies proof via Light Client
5. Transfers assets to user

## Example: Transfer 100 USDC from Ethereum to BSC

**Step 1: Lock on Ethereum**

Alice calls `transferOutToken` on Ethereum MOS, locking 100 USDC.

**Step 2: Relay**

Messenger automatically relays the transaction through MAP Relay Chain.

**Step 3: Receive on BSC**

Alice receives 100 USDC on BSC. She only sent one transaction.

## Contract Interface

```solidity
interface IMOS {
    // Transfer token to another chain
    function transferOutToken(
        address _token, 
        bytes memory _to, 
        uint256 _amount, 
        uint256 _toChain
    ) external;
    
    // Transfer native token to another chain
    function transferOutNative(
        bytes memory _to, 
        uint256 _toChain
    ) external payable;
    
    // Deposit token to vault (provide liquidity)
    function depositToken(
        address _token, 
        address _to, 
        uint256 _amount
    ) external;
    
    // Deposit native token to vault
    function depositNative(address _to) external payable;
    
    // Check if token is bridgeable to target chain
    function isBridgeable(
        address _token, 
        uint256 _toChain
    ) external view returns (bool);
}
```

## Usage Example

```solidity
// Transfer 100 USDC to BSC
IERC20(usdcAddress).approve(mosAddress, 100e6);
IMOS(mosAddress).transferOutToken(
    usdcAddress,           // token
    abi.encodePacked(to),  // receiver on target chain
    100e6,                 // amount
    56                     // BSC chain id
);

// Transfer native token (e.g., ETH) to BSC
IMOS(mosAddress).transferOutNative{value: 1 ether}(
    abi.encodePacked(to),  // receiver
    56                     // BSC chain id
);
```

## Contract Addresses

See [v1 Contracts](../../network/v1-contracts.md) for MOS contract addresses on supported chains.
