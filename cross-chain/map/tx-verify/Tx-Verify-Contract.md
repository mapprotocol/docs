# Tx-Verify-Contract

## Contract Address

tx verify contract is deployed at address:

```
```

## Contract interface

```solidity
    function verifyProof(proveData memory _proveData, bytes memory aggPk) external view returns (bool success, string memory message);
        
    function UpdateBlockHeader(blockHeader memory bh, bytes memory aggPk) external;
        
    function init(uint _threshold, bytes[] memory _pairKeys, uint[] memory _weights,uint round) external;
```

## Interact with contract interface


### init

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| _threshold   | uint      | validators count |
| _pairKeys     | bytes[]      | validators public keys |
| _weights     | uint[]      | voting power |
| round     | uint     | Committee rounds |



### UpdateBlockHeader

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| _bh   | blockHeader     | the new round block header |
| aggPk     | bytes      | the agg public key |



### verifyProof

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| _proveData   | proveData      | the proof of transaction |
| aggPk     | bytes      | the agg public key |

#### output parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| success | bool          | if the verification is successful, is true |
| message | string        | if the verification is successful, is empty |



