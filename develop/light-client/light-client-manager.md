# Client Manager



## what is light client manager?

Client Manager is just a management contract. Every time MAPO supports a chain cross-chain, it will deploy the corresponding Light Client contract on the MAP Relay Chain. In order to avoid unnecessary errors when synchronizing different Light Client blocks, we created a Client Manager contract to synchronize blocks, and it is also more convenient to verify the ProofData of different Light Clients.

## How it works?

Client Manager is a management contract that relies on the owner to register the contract addresses of other Light Clients on the MAP Relay Chain. Detailed verification logic and block data are still updated on the Light Client contract. Client Manager is only to provide more convenient update blocks and Proof verification

## Client Manager Interface

Here is the interface of the Client Manager method

```
interface ILightClientManager {
	//update block header
    function updateBlockHeader(uint256 _chainId, bytes memory _blockHeader) external;
    //Verify transaction proof
    function verifyProofData(uint _chainId, bytes memory _receiptProof) 
    external
    view 
    returns (bool success, string memory message,bytes memory logs);
    //Get the current update block height of Light Client
    function headerHeight(uint256 _chainId) external view returns (uint256);
    //Get verifiable block transaction range
    function verifiableHeaderRange(uint256 _chainId) external view returns (uint256, uint256);
}
```