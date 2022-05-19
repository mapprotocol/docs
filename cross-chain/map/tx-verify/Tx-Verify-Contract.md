# Tx-Verify-Contract

## Contract Address

tx verify contract is deployed at address:

```
```

## Contract interface

```solidity
     function verifyProofData(proveData memory _proveData, G2 memory aggPk) external view returns (bool success, string memory message);

    function updateBlockHeader(blockHeader memory bh, G2 memory aggPk) external;

    function init(uint _threshold, G1[] memory _pairKeys, uint[] memory _weights,uint round) external;

```

## Interact with contract interface


### init

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| _threshold   | uint      | validators count |
| _pairKeys     | G1      | validators public keys |
| _weights     | uint[]      | voting power |
| round     | uint     | Committee rounds |



### UpdateBlockHeader

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| _bh   | blockHeader     | the new round block header |
| aggPk     | G2      | the agg public key |



### verifyProofData

judge whether the transaction is true and valid by verifying the transaction receipt

#### input parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| _proveData   | proveData      | the proof of transaction |
| aggPk     | G2      | the agg public key |

#### output parameters

| parameter| type         | comment |
| -------- | ------------ | ------- |
| success | bool          | if the verification is successful, is true |
| message | string        | if the verification is successful, is empty |



