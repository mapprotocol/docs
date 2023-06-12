！[](precompile.png)

預編譯合約是EVM中使用的一種折衷方法，用於提供更復雜的庫函數（通常用於複雜的操作，如加密、散列等），這些函數不適合用操作碼來編寫。它們適用於那些簡單但經常被調用的合約，或者邏輯上固定但計算密集的合約。預編譯合約是在客戶端用客戶端代碼實現的，由於它們不需要EVM，所以運行速度很快。對開發者來說，它的成本也比使用直接在EVM中運行的函數低。

欲瞭解更多細節： ![(precompile.png)](https://docs.maplabs.io/develop/map-relay-chain/precompile-contract)