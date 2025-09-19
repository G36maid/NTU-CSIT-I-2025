# Week 1 - Introduction to Computer Systems and Security Research

**授課教師**: 黎士瑋教授  
**日期**: 2025/9/5  
**主題**: Operating Systems 與 Systems Security 研究介紹

## 課程目標

- 介紹作業系統研究
- 介紹系統安全研究

## 背景知識需求

- Systems Programming
- Operating Systems
- Computer Architecture/Organization
- Computer Security

## 授課教師介紹

**黎士瑋教授 (Assistant Professor)**
- 台大資工系
- Columbia University 電腦科學博士
- 主持 Secure Systems Lab (R404)
- 研究興趣：作業系統、電腦安全、電腦架構
- 曾與開源社群合作（Linux KVM hypervisor）及產業界（MediaTek、Arm）

### Secure Systems Lab (SSLAB)

- 20+ 學生積極參與（碩士生與大學生）
- 研究興趣：建構安全的系統軟體、硬體安全
- 主要專注於 Linux kernel 研究
- 以防禦方法為主，也進行攻擊研究

## 現代電腦系統架構

### 系統組成
```
Application Programs
Libraries
Operating System
Hardware
```

### 核心概念

**抽象化與模組化的重要性**：
- 封裝複雜性
- 簡化維護與部署
- 提高安全性

**範例**：
- Libraries 和 protocols 簡化程式設計
- Compilers 支援高階語言程式設計
- Operating systems 提供 process/thread 抽象
- Hypervisors 支援 VM 抽象

## 作業系統 (Operating Systems)

### 主要服務

- 執行/停止程式
- 檔案系統：開啟/讀取/寫入/關閉/建立/刪除檔案
- 取得當前時間
- 系統工具與管理工具

### OS vs OS Kernel

- **OS**：包含更廣泛的功能，通常包含 OS kernel
- **OS Kernel**：直接管理硬體資源並提供核心服務

## Linux Kernel 深入探討

### 歷史背景

- 1991年 Linus Torvalds 開發第一版 Linux
- 針對 Intel 80386 微處理器
- 廣泛部署於嵌入式系統、行動裝置（Android）、個人電腦、伺服器、超級電腦

### 取得 Linux 原始碼

```bash
# 從 Linus 的 repository clone
git@github.com:torvalds/linux.git
```

**官方連結**：
- [kernel.org](https://kernel.org)
- [Linus Torvalds' Linux Repository](https://github.com/torvalds/linux)

**閱讀工具**：
- [https://elixir.bootlin.com/linux/latest/source](https://elixir.bootlin.com/linux/latest/source)
- cscope + vim plugin

### Linux Kernel 版本類型

1. **Mainline**: Linus Torvalds 維護，新功能開發，每9-10週發布
2. **Stable**: mainline kernel 發布後視為穩定版
3. **Prepatches (RC kernels)**: mainline kernel 預發布版本
4. **Long-term Support (LTS)**: 長期支援版本，適合產業使用

### Linux 程式碼結構

主要目錄說明：
- `kernel/`: 核心功能程式碼（process/thread、scheduler、locking）
- `mm/`: 記憶體管理
- `fs/`: Virtual File System (VFS) 和檔案系統驅動程式
- `net/` & `block/`: 網路協定堆疊和區塊 I/O
- `virt/`: Kernel Virtual Machine (KVM) 支援
- `arch/`: 架構特定程式碼（arm64、ia32/64、risc-v）

### 編譯 Linux Kernel 步驟

1. 取得 Linux kernel 原始碼
2. 設定 kernel（使用預定義設定或 UI 設定）
3. 使用 `make` 編譯（可用 `-jn` 加速）
4. 執行 `make modules_install` 安裝 kernel modules
5. 執行 `make install` 複製 kernel binary 到 /boot

### 測試編譯的 Kernel

- 在虛擬機器（VM）中執行
- 選擇 VM 實作（如 QEMU）
- 設定 VM 執行 kernel
- **注意**：無法在容器中執行 kernel

## 虛擬化與虛擬機器

### Hypervisors 架構

```
App App       |  App App App ...
Operating     |  Operating System
Systems       |  Hardware
Virtual       |
Machine       |
...           |
Hypervisor    |
Hardware      |
```

### 虛擬化定義

- 將虛擬介面/資源對應到實體資源
- 虛擬和實體介面可相同或不同
- 可支援比實體更多的虛擬副本

### VM 使用案例

- 雲端運算的核心技術
- 廣泛應用：雲端、桌面、行動、汽車
- 多 OS 環境、測試新 CPU 架構
- 隔離：惡意軟體分析、沙箱、提高資源利用率

### System Virtual Machines

- 虛擬化 ISA
- 提供完整系統環境執行 OS kernel
- 可執行相同或不同 ISA

**範例**：VMware Workstation、Virtual PC、QEMU、KVM

### ARM 虛擬化

支援硬體層級的虛擬化功能

### Hypervisor 設計與實作

**核心功能**：
- CPU 虛擬化
- 記憶體虛擬化
- I/O 虛擬化

**設計目標**：
- 功能正確性（必須）
- 良好效能（目標）

### KVM 案例研究

- 整合於 Linux kernel
- 以 kernel module 實作 KVM 功能
- 與 QEMU 合作建立和執行 VM
- 被 Google、Amazon 等公司使用

## 作業系統研究方向

### 研究興趣領域

1. **虛擬化**
   - 雲端和嵌入式系統的新興應用
   - 效能最佳化
   - 安全性

2. **具體行動**
   - 開發新 OS 或 OS 機制讓 VM 執行更快
   - OS kernel 當機復原

### 研究案例 1：Unikernels

**挑戰**：VM 相比容器效能和擴展性較差（GB vs MB 規模）

**Unikernels 特色**：
- 專用、單一位址空間的機器映像
- 僅支援目標應用程式需要的 kernel 功能
- 相對於通用 OS kernel（Linux、Windows）

**Unikraft 案例**：
- 使用模組化實現專業化
- 將 OS 功能分割為細粒度元件
- 透過定義良好的 API 邊界通訊

**目前研究**：
- ARM-based VM 的 Unikernels
- 高效管理：更快的硬體間遷移
- 機密 VM 的新興應用案例
- 更好的效能：較 Linux 簡單、啟動時間更快

### 研究案例 2：OS Kernel 當機復原

**問題**：OS kernel 中的單一錯誤可能導致整個系統當機

**解決方案**：基於 checkpoint+restore 機制的 Linux 原型
- 能夠從驅動程式錯誤中復原
- 挑戰：巨大的效能開銷
- 需要更多測試以確保安全保證

## 實驗室專題研究

### 要求
- **程式語言**：精通 C 語言和 Linux 環境
- **先修課程**：系統程式設計、作業系統
- **強烈建議**：電腦安全、電腦架構、虛擬機器

### 訓練內容
- 商用系統軟體內部實作經驗（如 Linux KVM hypervisor）
- 低階系統設計
- 研讀重要學術論文
- 批判性與全面性思考

### 研究經驗

**優點**：
- 建構在硬體上運行的真實軟體
- 參與開源專案如 Linux
- 產生真實影響

**挑戰**：
- 學習曲線陡峭（需要多領域背景知識）
- 系統軟體建構困難
- 除錯時間可能比編碼時間更長

### 專題範例
**將 Linux 移植到 Mac mini**
- Asahi Linux 支援 Mac M1-M3 環境
- 正在進行將 KVM variant 移植到 Asahi Linux

### 研究心得
- 系統研究是有回報的經驗
- 需要探索未解決的挑戰
- 通常需要超過一學期才能產出好的成果
- 可能花很多時間在實驗設定上
- 閱讀第一篇論文需要大量背景知識

## 重要聯絡資訊
- **Email**: [shihwei@csie.ntu.edu.tw](mailto:shihwei@csie.ntu.edu.tw)
- **實驗室**: Secure Systems Lab (R404)
- **網站**: [https://www.csie.ntu.edu.tw/~shihwei](https://www.csie.ntu.edu.tw/~shihwei)