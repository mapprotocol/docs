# MOS 是如何工作的

## 術語表

### MOS中繼鏈

MOS中繼是MAP中繼鏈上的主合約，主要負責處理MAP中繼鏈上的一切。 其主要功能如下：
- 處理用戶的跨鏈轉賬

- 處理Maintainer的跨鏈轉移

- 負責調用LightNodeManager合約來驗證消息並分析相關事件

- 負責其他鏈MOS合約的交易轉發

- 負責管理令牌的發行和回收以及令牌的Vault權限

- 負責手續費的處理

  

### MOS

MOS是源鏈上的主合約。 主要負責處理來自MAP Relay Chain的東西。 其主要功能如下：
- 處理用戶的跨鏈轉賬

- 處理Maintainer的跨鏈轉移

- 負責調用LightNode合約驗證MAP中繼鏈上的事物，分析相關事件

  

### 金庫

Vault是每個跨鏈代幣對應的權益代幣合約，其主要功能如下；
- 質押用戶的流動性，發行股權代幣VToken
- 記錄跨鏈費用並平均分配給流動性提供者
- 流動性的提取和轉移

### 費用

Fee 是用戶跨鏈手續費的收取和管理合約，在 MOS Relay 合約中進行分發。 其主要功能如下：

- 設置保險庫、中繼和協議的費用分配比例
- 設定手續費的收費標準

### 證明驗證

證明驗證是證明證明跨鏈數據是合法的。 一般的驗證流程封裝在LightNode中，流程如下：
- 證明 Proof 可以在交易主體中被驗證
- 證明交易主體可以構造ReciptRoot
- 證明 ReciptRoot 在頭部
- 根據保存在LightNodes中的Header集合驗證Header的合法性

## 如何運行

![MOS Flow](mosFlowChart.png)

### 代幣轉移

**轉出**

跨鏈轉出的主要流程如下：

- 用戶授權資產扣除

- 用戶調用合約指定跨鏈chainId和數量，合約扣除相應數量

- 合約映射目標鏈的token，並根據token小數計算扣除的服務費和數量（MOS Relay）

- 扣除手續費並根據TokenRegister註冊信息分配（MOS Relay）

- 生成出入訂單信息和transferOut事件輸出



**轉接中繼**

Maintainer監控信息轉出並調用相應的transferIn的主要流程如下：

- 掃描MOS/MOS Relay的transferOut事件

- 根據Event和Chain生成相應的Proof

- 將證明數據和事件形成交易調用目標鏈的transferIn



**轉入**

    Maintainer跨鏈轉入的主要流程如下：

- 證明驗證

- 解析對應的transOut數據

- 合約映射目標鏈的token，並根據token小數計算扣除的服務費和數量（MOS Relay）

- 扣除手續費並根據TokenRegister註冊信息分配（MOS Relay）

- 判斷是否是到MAPO鏈的轉賬，如果是下一步，如果不是out信息產生的訂單和產生的transferOut事件輸出（MOS Relay）

- 將跨鏈資產轉出給用戶



### 保管庫存款



**存入**

    MOS鏈用戶質押流動性的主要流程如下：

- 用戶授權資產扣除

- 用戶調用合約指定跨鏈chainId和數量，合約扣除相應數量

- 將處置信息生成訂單並生成處置Out事件輸出

  

**存入**

MOS（Maintainer）質押流動性初始申請流程如下：

- 證明驗證

- 解析對應的depositOut數據

  

MOS Relay鏈用戶質押流動性的初始流程如下：

- 用戶授權資產扣除

- 用戶調用合約指定跨鏈chainId和數量，合約扣除相應數量

  

MOS (Maintainer)/MOS Relay背後的流程如下：

- 根據用戶質押的數量生成對應的權益代幣Vtoken給用戶

- 生成對應的depositIn事件