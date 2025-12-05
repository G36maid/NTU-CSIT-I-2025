# Week 7: Privacy-Preserving Computing in the AI Era

- **授課教師**: Wei-Chao Chen (Content by Yu-Te Ku)
- **主題**: Exploring techniques for data analysis and machine learning that protect data privacy.

隨著 AI、大數據、物聯網 (IoT) 的發展，資料隱私面臨嚴峻挑戰。本週課程介紹了在 AI 時代保護資料隱私的三種關鍵技術：同態加密 (Homomorphic Encryption, HE)、安全多方計算 (Secure Multiparty Computation, MPC)，以及可信執行環境 (Trusted Execution Environments, TEE)。

---

## 1. 資料隱私的挑戰與法規

- **資料隱私**: 指的是對個人資訊的適當處理、加工、儲存和使用。
- **全球法規**:
  - **GDPR (歐盟通用資料保護規則)**: 嚴格規範資料的收集與處理。
  - **CCPA (加州消費者隱私法)**: 賦予加州居民對其個人資訊更多的控制權。
- **主要挑戰**:
  - **資料外洩**: 網路攻擊日益精密。
  - **使用者同意**: 隱私政策複雜，難以取得有效的知情同意。
  - **資料去識別化**: 有效地匿名化資料以保護個人身份充滿挑戰。

---

## 2. 三種關鍵的隱私保護計算技術

這三種技術旨在實現「在使用中保護資料 (protects data in use)」，即在不洩露原始資料的情況下進行計算。

| 技術 | Homomorphic Encryption (HE) | Secure Multiparty Computation (MPC) | Trusted Execution Environments (TEE) |
| :--- | :--- | :--- | :--- |
| **核心概念** | 在加密資料上直接進行計算。 | 多方協作計算一個函式，但各方無法得知彼此的輸入資料。 | 利用硬體隔離區 (enclave) 來保護程式碼和資料的機密性與完整性。 |
| **隱私保證** | 純密碼學保證。 | 密碼學保證，依賴於參與方之間不共謀的假設。 | 依賴於硬體的可信度，需防禦旁通道攻擊 (Side-Channel Attacks)。 |
| **效能瓶頸** | **計算密集 (Compute-bound)**。 | **網路密集 (Network-bound)**。 | 接近明文計算的效能。 |
| **互動性** | 非互動式 (Non-interactive)。 | 高度互動式。 | 非互動式。 |

根據 Gartner 的技術成熟度曲線 (Hype Cycle)，同態加密 (HE) 目前仍處於 **創新觸發期 (Innovation Trigger)**，顯示其巨大的潛力與尚待克服的挑戰。

---

## 3. 技術詳解

### Secure Multiparty Computation (SMPC)

SMPC 讓多個參與方（例如，A, B, C）可以共同計算一個函式 `F(A, B, C)`，而不需要一個可信的中央伺服器。參與方之間僅交換加密訊息，最終得到計算結果，但過程中任何一方都無法得知其他方的原始輸入資料。

- **範例：秘密加法**: 兩方（Alice 和 Bob）想計算他們薪水的總和，但不告訴對方自己的薪水。Alice 可以將自己的薪水 `X` 加上一個隨機數 `R` 後傳給 Bob，Bob 將收到的數值加上自己的薪水 `Y` 後再傳回給 Alice，最後 Alice 減去隨機數 `R` 即可得到總和 `X+Y`。
- **實作框架：CrypTen**:
  - Facebook (Meta AI) 開源的研究工具，專為安全的機器學習設計。
  - 基於 PyTorch，讓熟悉 PyTorch 的開發者能輕易上手。
  - 透過將 PyTorch 模型轉換為安全的多方計算協定來實現隱私保護的機器學習。
  - **效能**: 效能受**網路頻寬**影響極大。在 MNIST 訓練任務上，1 Gb/s 頻寬下的總時間約為 78 秒，而 100 Mb/s 頻寬則需 127 秒。

### Homomorphic Encryption (HE)

HE 允許直接在密文上進行運算，其結果解密後與在明文上進行相同運算的結果一致。`Enc(x) + Enc(y) = Enc(x+y)`。

- **核心挑戰：噪音累積**: 為了安全性，HE 在加密時會加入微小的隨機噪音。每次運算（特別是乘法）都會導致噪音增長，當噪音大到一定程度時，解密就會失敗。
- **Bootstrapping**: 是一種「為密文去噪」的技術，可以重設密文中的噪音，使其能支援無限次的運算。支援 Bootstrapping 的方案稱為**全同態加密 (Fully Homomorphic Encryption, FHE)**。然而，Bootstrapping 本身是一個計算成本極高的操作，比普通的同態加/乘法慢約 **10,000 倍**，是 FHE 效能的主要瓶頸。
- **主要方案**:
  - **BGV / BFV**: 支援整數運算。
  - **CKKS**: 支援實數（浮點數）的近似運算。
  - **FHEW / TFHE**: 支援位元運算（邏輯閘），適合用來實現任意函式。
- **實作函式庫**: TenSEAL (基於 Microsoft SEAL), OpenFHE。

### FHE-DNN vs. SMPC-DNN 效能比較

以下是在 VGG9 模型和 CIFAR-10 資料集上的推論 (inference) 效能比較：

| 方法 | 總時間 (單一樣本) |
| :--- | :--- |
| **明文推論 (PyTorch)** | **0.004 秒** |
| **MPC-DNN (CrypTen)** | **1.21 秒** |
| **FHE-DNN (FHEW/TFHE)** | **86 秒** |

**結論**: 目前在深度學習推論場景下，SMPC 的效能遠高於 FHE。

### Trusted Execution Environments (TEE)

TEE 是 CPU 內的一個硬體隔離安全區，可以保護在其中執行的程式碼和資料不被外部（包括作業系統和 Hypervisor）窺探或竄改。

- **核心能力**:
  - **隔離執行 (Isolated Execution)**: 程式在安全區內執行。
  - **密封儲存 (Sealed Storage)**: 資料加密後儲存在外部，只有同一個 TEE 能解密。
  - **遠端證明 (Remote Attestation)**: 允許遠端使用者驗證在 TEE 中執行的程式碼是否為預期且未經竄改的。
- **演進**:
  - **第一階段**: TPM (信賴平台模組)，提供信任根 (root of trust)。
  - **第二階段**: Enclave 模型，如 Intel SGX，提供處理程序級別的隔離。
  - **第三階段**: VM-based TEEs，如 AMD SEV-SNP、Intel TDX，提供虛擬機級別的隔離，更容易與現有雲端架構整合。
- **應用**:
  - **雲端機密運算**: 保護租戶的虛擬機不受雲端服務提供商的侵擾。
  - **金鑰管理**: 如 Android KeyStore，將私鑰儲存在 TEE 中。
  - **機密機器學習**: 在 TEE 中保護模型和輸入資料的隱私。
- **安全挑戰**: 雖然 TEE 能抵禦軟體攻擊，但仍面臨**旁通道攻擊 (Side-channel attacks)** 和**故障注入 (Fault injection)** 等硬體層面的威脅。
