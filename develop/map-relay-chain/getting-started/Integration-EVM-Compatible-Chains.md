# Integrate MAP Relay Chain with EVM-Compatible Chains

The cross-chain process of the MAP Protocol involves multiple steps, from locking assets to verifying data, ensuring secure transfer and interoperability of assets between different blockchains. Here we will discuss the integration process with EVM-compatible chains. The development and deployment of the following modules are sufficient to integrate with the MAP Protocol:

## Light-client

Both chains integrating with the MAP Protocol need to deploy each other's `light-client`. Since both chains are EVM-compatible, both `light-clients` will be implemented in Solidity to reduce the upgrade and maintenance efforts on the mainnets of both chains. To deploy the `light-client` contract of the `mapo-relay-chain` on the integrating chain, the chain should support precompiled instructions such as bls, bn254.

As the cross-chain messages between the integrating chain and other chains on the MAP Protocol are relayed through the `map-relay-chain`, the integrating chain only needs to deploy the `light-client` of the `map-relay-chain` to verify cross-chain messages from the `map-relay-chain`. Since `map-relay-chain` has already implemented the Solidity version of the `light-client`, the integrating chain only needs to focus on implementing its own Solidity `light-client`. The integrating chain's `light-client` should at least fulfill two functions:

+ Maintain and update the state of the `light-client`, which involves storing a certain number of block headers and continuously updating to verify new block headers.
+ Verify contract events on the source chain based on the current state of the `light-client`, usually validating transaction receipts (MPT verification information).

### Light-client Contract Development

To integrate with the MAP Protocol, the integrating chain needs to satisfy the [ILightNode interface](https://github.com/mapprotocol/map-contracts/blob/main/protocol/contracts/interface/ILightNode.sol) defined by the MAP Protocol. It should primarily satisfy the following interface:

```solidity

// Verify the validity of the transaction according to the header, receipt
// The interface will be updated later to return logs

function verifyProofData(bytes memory _receiptProof) external view returns (bool success, string memory message, bytes memory logs);

function updateBlockHeader(bytes memory _blockHeader) external;

function updateLightClient(bytes memory _data) external;

``` 

### Light-Client Deployment
   
+ Deploy the integrating chain's `light-client` contract on the `map-relay-chain`.
+ Deploy `map-relay-chain`'s `light-client` contract on the integrating chain. [more detials](https://github.com/mapprotocol/map-contracts/tree/main/mapclients).
  
### Maintainer Development

The Maintainer service is an independent program designed to update and synchronize the state of the `light-client`, and to submit corresponding block header data to the `light-client` on both the source and target chains. As the Maintainer service already supports the `map-relay-chain`, developers of the integrating chain only need to add support for their own chain within the Maintainer service. Integrating chain developers can fork the Maintainer provided by the Mapo Protocol's [Maintainer repository](https://github.com/mapprotocol/compass) and perform further development to add support for their own chain.

+ Get the current state of the `light-client`.
+ submit the header to the `light-client`.

## MAP Omnichain Service (MOS)

The MOS layer defines the general framework and implementation logic of the MAP Protocol's common cross-chain messaging. Developers of the integrating chain do not need to implement this module separately but can directly deploy and use it. The Mos layer needs to be deployed on both chains of the cross-chain pair. As EVM-compatible chains, we implement the Solidity version of Mos. Installation and deployment instructions can be found [here](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md).

Key data structures and[Interface](https://github.com/mapprotocol/mapo-service-contracts/tree/main/evm/contracts/interface):

```solidity
 enum MessageType {
        CALLDATA,
        MESSAGE
    }

    // @notice This is the configuration you need across the chain.
    // @param relay - When it is true, the relay chain is required to perform a special execution to continue across the chain.
    // @param msgType - Different execution patterns of messages across chains.
    // @param target - The contract address of the target chain.
    // @param payload - Cross-chain content.
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    // @param value - Collateral value cross-chain, currently not supported, default is 0.
    struct MessageData {
        bool relay;
        MessageType msgType;
        bytes target;
        bytes payload;
        uint256 gasLimit;
        uint256 value;
    }

    // @notice Gets the fee to cross to the target chain.
    // @param toChain - Target chain chainID.
    // @param feeToken - Token address that supports payment fee,if it's native, it's address(0).
    // @param gasLimit - The gasLimit allowed to be consumed by an operation performed on the target chain.
    function getMessageFee(uint256 toChain, address feeToken, uint256 gasLimit) external view returns(uint256, address);


    // @notice Initiate cross-chain transactions. Generate cross-chain logs.
    // @param toChain - Target chain chainID.
    // @param messageData - Structure MessageData encoding.
    // @param feeToken - In what Token would you like to pay the fee.
    function transferOut(uint256 toChain, bytes memory messageData, address feeToken) external payable  returns(bytes32);


    // @notice Add the fromaddress permission.
    // @param fromChain - The chainID of the source chain.
    // @param fromAddress - The call address of the source chain.
    // @param tag - Permission,false: revoke permission.
    function addRemoteCaller(uint256 fromChain, bytes memory fromAddress, bool tag) external;

    // @notice Query whether the contract has execution permission.
    // @param mosAddress - This is the mos query address.
    // @param fromChainId - The call chain id of the source chain.
    // @param fromAddress - The call address of the source chain.
    function getExecutePermission(address mosAddress,uint256 fromChainId,bytes memory fromAddress) external view returns(bool);

    event mapMessageOut(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData);

    event mapMessageIn(uint256 indexed fromChain, uint256 indexed toChain, bytes32 orderId, bytes fromAddrss, bytes callData, bool result, bytes reason);
```


### MOS Contract Development
   
Deploying the mos contract on both the integrating chain and the map-relay-chain involves the following steps:

1. Deploying mos on the Integrating Chain:

Deploy the mos contract on the integrating chain by following these steps:

   + Access the mos contract [repository](https://github.com/mapprotocol/mapo-service-contracts) on GitHub.
   + Navigate to the `evm` folder.
   + Deploy the mos contract by following the deployment instructions provided in the [README](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md).
  
2. Deploying mos on the Map-Relay-Chain:

Similar to the steps for the integrating chain, you need to deploy the mos contract on the map-relay-chain:

+ Access the mos contract [repository](https://github.com/mapprotocol/mapo-service-contracts) on GitHub.
+ Navigate to the `evm` folder.
+ Deploy the mos contract by following the deployment instructions provided in the [README](https://github.com/mapprotocol/mapo-service-contracts/blob/main/evm/README.md).

After deploying the mos contract on both the integrating chain and the `map-relay-chain`, you will need to set the `light-client` contract addresses for both chains using the `setLightClient` method. This connection is crucial for the successful operation of the cross-chain messaging process.


### Messenger Development

The Messenger service is an independent program designed to monitor and route specific events from the mos contract on both the source chain and the target chain. These events include common messaging events such as `mapMessageOut` and `mapMessageIn`. The Messenger service constructs corresponding proof data for these events and ultimately submits cross-chain messages along with proof data to the target chain. Since the Messenger service already supports the `map-relay-chain`, developers of the integrating chain only need to add support for their own chain within the Messenger service. Developers can fork the Messenger service provided by the Mapo Protocol's [Messenger repository](https://github.com/mapprotocol/compass) and customize it to add support for their own chain.

In this process, the Messenger service plays a crucial role in facilitating the transfer of cross-chain messages and their associated proofs, ensuring the secure and reliable transmission of information between the source and target chains. It abstracts the complexity of interacting with smart contracts and handling events, making it easier for developers to integrate their chains into the Mapo Protocol framework.

## Application Layer

The application layer represents the true business logic of the cross-chain framework. Users define specific business logic at this layer, such as asset management and operations like lock, unlock, mint, burn, and more. The actual cross-chain operations take place within the application layer, where the `transferOut` interface from the Mos layer is invoked to write specific cross-chain messages onto the chain. [details](develop/mos/examples/omni-app.md)

Here's how the cross-chain process flows within the application layer:

+ User Interaction: Users interact with the application layer to initiate cross-chain actions, such as locking, unlocking, or transferring assets between chains.

+ Invoke transferOut: When a user initiates a cross-chain action, the application layer invokes the `transferOut` interface from the `Mos` layer. This interface constructs and formats the cross-chain message, including details of the action to be performed on the target chain.

+ Relay Message: Once the cross-chain message is constructed, the Messenger service is notified. The Messenger service gathers the necessary proof data and submits the cross-chain message along with the proofs to the target chain.

+ Target Chain Verification: On the target chain, the `light-client` deployed for the source chain is used to verify the authenticity and legality of the received cross-chain message. The `light-client` ensures that the data is consistent with the source chain's data, confirming the message's validity.

+ Execution and Action: After successful verification, the application layer on the target chain decodes the received message and performs the corresponding action, such as minting new tokens, unlocking locked assets, etc.

The application layer acts as the bridge between the user's intent and the technical complexities of cross-chain communication. It provides a user-friendly interface where users can trigger cross-chain operations and ensures that these operations are securely executed and verified across the involved chains.
























