# 客戶端管理



## 什麼是輕客戶端管理器？

Client Manager只是一份管理合約。 MAPO每支持一條鏈跨鏈，都會在MAPO鏈上部署對應的Light Client合約。 為了避免同步不同Light Client區塊時出現不必要的錯誤，我們創建了一個Client Manager合約來同步區塊，同時也更方便驗證不同Light Client的ProofData。

## 如何運行？

Client Manager 是一個管理合約，它依賴於所有者在 MAPO 鏈上註冊其他 Light Client 的合約地址。 詳細的驗證邏輯和區塊數據仍然在輕客戶端合約上更新。 Client Manager只是為了提供更方便的更新區塊和Proof驗證

## 客戶端管理界面

這是Client Manager方法的界面

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