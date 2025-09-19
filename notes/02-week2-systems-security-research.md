# Week 2 - Introduction to Systems Security Research

**授課教師**: 黎士瑋教授  
**日期**: 2025/9/12  
**主題**: Systems Security 研究介紹

## 課程目標

- 介紹系統安全研究
- 專題研究介紹（續）

## 電腦安全基本概念

### 常見安全威脅

- 假消息/假資訊
- 帳號/密碼竊取
- 劫持（電腦、網站、螢幕）
- 惡意軟體/病毒
- 勒索軟體
- CTF 競賽

### 攻擊案例：FORCEDENTRY

- NSO Group 的 iMessage zero-click 攻擊
- Zero-day 漏洞：之前從未被發現的錯誤
- 顯示僅保護密碼已不足夠

### 開源軟體的安全優勢

開源軟體有助於安全的原因：
- 透明度：程式碼可被檢視
- 社群審查：多人檢查程式碼
- 快速修補：漏洞可被快速發現和修復

## 安全的 CIA 三元組

### Confidentiality（機密性）
- 保護資訊免於未授權存取
- 確保敏感資料機密性
- 保證只有授權個人可存取

### Integrity（完整性）
- 維護資訊的一致性、準確性和真實性
- 防止未授權方修改

### Availability（可用性）
- 確保授權個人可存取資料且中斷最少
- 在需要時提供資訊、基礎設施和應用程式

## Trusted Computing Base (TCB)

### 定義
- 由 Butler Lampson 等人首次定義
- 電腦系統中安全依賴的少量軟體和硬體
- 與大量可能異常但不影響安全的部分區分開來

### 特性
- TCB 通常執行系統安全政策
- TCB 內的錯誤可能危及整個系統安全
- 包含軟體、硬體元件或兩者
- 軟體 TCB 假定為可信且無錯誤
- 可信度以 TCB 的程式碼行數 (LOC) 衡量

### TCB 範例
- 硬體（如 CPU）
- OS kernel：執行檔案/擁有者權限
- Hypervisor：執行 VM 隔離

## 建構安全系統

### 四個關鍵要素

1. **Goal（目標）**: 系統試圖達成什麼
2. **Threat Model（威脅模型）**: 假設攻擊者能做什麼
3. **Policy（政策）**: 讓系統達成目標的規則/計畫
4. **Mechanism（機制）**: 系統用來執行政策的軟體和硬體

### 案例研究：檔案保護

**基本場景**:
- 目標：只有特定使用者（Alice）能讀取檔案 F 中的秘密
- 威脅模型：攻擊者可猜測密碼但無法竊取伺服器
- 政策：保護 F 只有擁有者可讀取
- 機制：使用者 ID/帳號、密碼、檔案權限

**更強的攻擊者**:
- 威脅模型：攻擊者可利用 OS kernel 錯誤獲得 kernel 控制權
- 政策：確保檔案 F 在磁碟上加密 + OS kernel 無法存取記憶體中的檔案 F
- 機制：檔案系統加密 + 限制 OS kernel 的記憶體存取

**解決方案**:
1. 使用 page tables（無效，因為 OS kernel 管理 page tables）
2. 使用薄軟體層（TCB）調解 OS 和使用者互動
3. 使用 Hypervisor 架構

**物理攻擊者**:
- 威脅模型：攻擊者可物理存取電腦（如拔除 RAM）
- 機制：硬體加密（Intel SGX）

## 硬體加密：Intel SGX

### 特性
- 支援加密 enclave 在 OS kernel 上執行使用者程序
- 控制監督軟體（OS/hypervisor）的攻擊者只能存取加密資料/程式碼
- 提供軟體和物理攻擊保護
- RAM 拔除時內容仍保持加密

## 特權攻擊者的威脅

### 為什麼要擔心特權攻擊者？

- 攻擊者控制特權系統軟體（OS kernel 或 hypervisor）
- 可執行多種攻擊：
  - Keylogger
  - System call hooking
  - 拒絕服務攻擊
- 影響所有使用者
- 可能不被察覺（rootkits）

## 系統安全研究方向

### 目標與方法

- **目標**: 讓系統軟體更安全
- **威脅模型**: 通常假設強大的特權攻擊者
- **政策與機制**:
  - **Hardening**: 讓軟體更難被駭（如 ASLR）
  - **Attack isolation**: 限制攻擊者能力（如 Intel SGX）
  - **Runtime monitoring**: 主動系統探測/檢查（如防毒軟體）

## 記憶體安全問題

### 軟體錯誤統計
- Microsoft：70% 的安全錯誤都是記憶體安全問題

### 記憶體安全錯誤類型

**常見術語**：
- Buffer overflow
- Null pointer dereferences
- Stack and heap exhaustion/corruption
- Use after free
- Double free

**分類**：
1. **Temporal safety**: Use after free, double free
2. **Spatial safety**: Buffer overflow, stack and heap corruption

### Buffer Overflow 攻擊

堆疊佈局示例：
```
High address    +------------------+
                |  main()'s frame  |
                +------------------+
                |  return address  |
                +------------------+
    %rbp -----> |    saved %rbp    |
                +------------------+
                |        i         |
                +------------------+
                |      buf[127]    |
                |       ...        |
    %rsp -----> |      buf[0]      |
Low address     +------------------+
```

**防護措施**：
- 將堆疊標記為不可執行
- Stack canary
- 隨機化程序記憶體佈局
- Shadow stack

### Return Oriented Programming (ROP)

**程式碼重用攻擊**：
- 利用現有程式碼片段進行攻擊
- 避開不可執行堆疊保護

**防護措施**：
- 控制流完整性 (Control Flow Integrity)
- 保護函數返回、函數指標、例外處理
- 通常有效能開銷

**更複雜的變種**：
- Data-oriented programming：透過修改程式使用的資料改變執行流程

## 記憶體安全防護

### 程式語言層面
- 使用記憶體安全的程式語言（如 Rust）

### 硬體方法
- Intel CET
- Arm's CHERI, MTE, PA

### 軟體方法
- Address Sanitizer (ASAN)
- Address Space Layout Randomization (ASLR)
- Stack canary

## Rust 程式語言

### 特性
- 為效能和安全設計
- 可與現有 C 程式碼整合、編譯和連結
- Rust 程式可使用 C 函數/符號，反之亦然

### 安全優勢
- 編譯時執行安全檢查
- 禁止型別不匹配或原始指標（在安全 Rust 中）
- 自動調整大小的向量和字串防止 buffer overflow
- 執行生命週期和所有權：消除時間和空間記憶體安全問題

### Rust for Linux
- 將 Rust 整合到 Linux kernel 開發中

## Fuzzing 技術

### 定義
自動化軟體測試技術，提供無效、意外或隨機資料作為程式輸入，監控異常如當機、程式碼斷言失敗或記憶體洩漏。

### 目標
- 找出程式會出錯的方式
- 效能瓶頸通常是問題所在

## 機密運算 (Confidential Computing)

### 安全問題

**使用者資料隱私洩露**：
```
App App App | App App App 😈
OS Kernel   | OS Kernel
    VM      |     VM
        Hypervisor
        Hardware
```

**威脅**：
- 被入侵的 OS kernel/hypervisor 可完全存取使用者資料
- 雲端管理員惡意行為
- 可進行的攻擊：
  - 將 VM 記憶體傾印到檔案
  - 清除 VM 磁碟檔案
  - 攔截網路 I/O 流量
  - 安裝惡意軟體到 VM

### 硬體加密解決方案

**AMD SEV (Secure Encrypted Virtualization)**：
- 提供 TEE (Trusted Execution Environment)
- 硬體層級的記憶體加密
- 保護 VM 免受惡意 hypervisor 攻擊

**Arm CCA (Confidential Compute Architecture)**：
- Arm-v9-A 架構引入
- 確保惡意 hypervisor 無法存取安全 VM 的記憶體和 CPU 資料
- 依賴 RMM 執行存取控制保護政策

## 實驗室研究方向

### 目前研究主題
- 機密運算
- 虛擬化、OS kernel 和軟體安全

### 實驗室運作

**會議**：
- 每個專案的定期會議（週會/隔週會議）
- 系統/安全論文討論研討會

**研討會**：
- 歡迎所有人參加
- 期望貢獻並主導至少一篇論文討論
- 目前每週五中午舉行

**資源分享**：
- 透明的資源分享
- 在 GitHub repository 託管實驗室專案

### 實驗室優勢
- 系統/安全研究的獨特接觸
- 實作導向的豐富經驗
- 與頂尖研究者合作機會
- 貢獻開源專案

## 研究者建議

### 成為系統/安全研究者的建議

1. **深度了解系統**：必須非常了解系統運作
2. **系統化知識**：訓練系統化知識並建立深度研究
3. **解決挑戰性問題的動機**
4. **批判性思考**：願意與他人辯論/衝突
5. **接受不完美**：有時沒有完美解決方案
6. **成為專家**：不讓他人（如 dcard）主宰你的想法

## 職涯發展

### 工程 vs 研究

**工程**：
- 維護或擴展現有專案的新功能
- 功能或效能錯誤的除錯
- 錯誤和效能測試
- 硬體移植、帶起、維護驅動程式

**研究**：
- 建構軟體原型並評估
- 論文閱讀與撰寫及發表
- 產生專利
- 安全分析

### 常見問題

- **Q**: 系統/安全領域的職涯軌跡/前景？
- **A**: 軟韌體工程師、資安研究員

- **Q**: 會被 AI 取代嗎？
- **A**: 需要持續學習和適應

- **Q**: 如何選擇工程或研究？
- **A**: 透過實習和參與實驗室研究專案來嘗試

## 聯絡資訊
- **Email**: [shihwei@csie.ntu.edu.tw](mailto:shihwei@csie.ntu.edu.tw)
- **實驗室**: Secure Systems Lab (R404)