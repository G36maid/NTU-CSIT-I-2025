# Week 5: GPGPU Programming Introduction

- **授課教師**: Wei-Chao Chen
- **主題**: General-Purpose GPU Programming (GPGPU) and CUDA Crash Course

本週課程由陳維超教授介紹 GPGPU 的基本概念、硬體架構演進，並提供 NVIDIA CUDA 程式設計的入門速成教學。

---

## 1. CPU vs. GPU 架構差異

- **CPU (Central Processing Unit)**:
  - **核心少而強**: 設計用於處理**延遲敏感 (latency-sensitive)** 的任務。擁有複雜的控制邏輯、大型快取，能以極快的速度完成單一或少數幾個執行緒。
  - **架構**: 少量核心 (Cores)，但每個核心功能強大。
  - **比喻**: 如同一輛超級跑車，能極快地將少量貨物從 A 點送到 B 點。

- **GPU (Graphics Processing Unit)**:
  - **核心多而簡**: 設計用於處理**吞吐量敏感 (throughput-sensitive)** 的任務。透過數百至數千個簡單的核心，同時執行大量並行計算。
  - **架構**: 大量核心，但每個核心相對簡單。
  - **比喻**: 如同一隊卡車車隊，雖然單輛速度不如跑車，但能同時運送大量貨物，總吞吐量遠超跑車。

| 特性 | CPU | GPU |
| :--- | :--- | :--- |
| **設計哲學** | 低延遲 (Low Latency) | 高吞吐量 (High Throughput) |
| **核心數** | 少 (數個 ~ 數十個) | 多 (數百個 ~ 數千個) |
| **浮點運算能力 (FLOPS)** | 較低 | 非常高 |
| **記憶體頻寬** | 較低 | 非常高 |
| **應用** | 序列任務、作業系統 | 大規模並行計算、圖形渲染 |

---

## 2. GPGPU 與 CUDA 簡介

- **GPGPU (General-Purpose GPU Programming)**: 利用 GPU 強大的並行計算能力，來執行圖形渲染以外的通用計算任務。這是現代超級計算、機器學習和人工智慧背後的驅動力。
- **CUDA (Compute Unified Device Architecture)**:
  - 由 NVIDIA 開發的並行計算平台與程式設計模型。
  - 提供了一組最小的 C/C++ 語言擴充，讓開發者能直接利用 GPU 的計算資源。
  - **工作流程**:
    1.  撰寫包含 C/C++ 和 CUDA 擴充語法的 `.cu` 檔案。
    2.  使用 CUDA 編譯器 `nvcc` 進行編譯。`nvcc` 會將程式碼分為兩部分：
        - **Host Code**: 在 CPU 上執行的標準 C/C++ 程式碼。
        - **Device Code**: 在 GPU 上並行執行的 Kernel 函式。
    3.  編譯後，Device Code 會被轉換為 PTX (Parallel Thread Execution) 中間碼，再由 CUDA 驅動程式在執行時期轉為硬體指令。

---

## 3. CUDA 程式設計模型核心概念

### C/C++ 語言擴充

- **函式執行空間指定**:
  - `__global__`: 表示這是一個 **Kernel 函式**，只能從 Host (CPU) 呼叫，並在 Device (GPU) 上執行。返回值必須是 `void`。
  - `__device__`: 表示這是一個 Device 函式，只能從 `__global__` 或其他的 `__device__` 函式中呼叫，並在 Device 上執行。
  - `__host__`: 表示這是一個 Host 函式，在 CPU 上執行（此為預設，可省略）。
- **Kernel 啟動語法**:
  - `my_kernel<<<num_blocks, threads_per_block>>>(args);`
  - `<<<...>>>` 語法用於指定 Kernel 執行的配置，包含要啟動多少個 **Block**，以及每個 Block 內有多少個 **Thread**。
- **變數類型限定詞**:
  - `__shared__`: 宣告一個位於 **Shared Memory** 的變數。此記憶體由同一個 Block 內的所有 Thread 共享，速度非常快。
  - `__constant__`: 宣告一個位於 **Constant Memory** 的變數，為唯讀快取記憶體。

### 執行緒階層 (Thread Hierarchy)

CUDA 將並行計算任務組織成一個三層的階層結構：
1.  **Grid**: 一個 Kernel 啟動的所有執行緒組成一個 Grid。
2.  **Block**: Grid 被劃分為多個 Block。**同一個 Block 內的執行緒可以互相合作**，透過 Shared Memory 共享資料，並可以透過 `__syncthreads()` 進行同步。
3.  **Thread**: Block 由多個 Thread 組成，這是執行的最小單位。

每個執行緒都可以透過內建變數得知自己的身份：
- `blockIdx.x`, `blockIdx.y`: 目前執行緒所在的 Block 在 Grid 中的索引。
- `threadIdx.x`, `threadIdx.y`: 目前執行緒在 Block 內的索引。
- `blockDim.x`, `blockDim.y`: Block 的維度大小。
- `gridDim.x`, `gridDim.y`: Grid 的維度大小。

一個常見的 1D 索引計算模式是：
`int idx = blockIdx.x * blockDim.x + threadIdx.x;`

### 記憶體模型 (Memory Model)

CUDA 提供多種記憶體空間，各有不同特性：
- **Registers**: 每個 Thread 私有，速度最快。
- **Shared Memory**: 每個 Block 私有，由 Block 內所有 Thread 共享，速度極快 (On-chip SRAM)。
- **Global Memory**: 所有 Thread 共享 (GPU 的 VRAM)，容量最大但速度最慢。
- **Constant Memory / Texture Memory**: 唯讀，有硬體快取。

**高效能程式的關鍵**在於最大化使用 Registers 和 Shared Memory，最小化對 Global Memory 的存取。

### 同步 (Synchronization)

- `__syncthreads()`: 這是一個 **Barrier**。Block 內的所有 Thread 執行到此處時會暫停，直到**所有** Thread 都到達這個 Barrier 後，才會繼續執行後續指令。這對於確保 Shared Memory 的讀寫順序至關重要。

---

## 4. GPU 硬體架構與 SIMT

- **演進**: GPU 從早期只能執行固定功能的圖形管線，演進為擁有**統一著色器 (Unified Shader)** 的可程式化架構。這些 Shader 就是通用的並行處理器，奠定了 GPGPU 的基礎。
- **SM (Streaming Multiprocessor)**: 現代 GPU 的核心建構單元。一個 Block 會被排程到一個 SM 上執行。
- **SIMT (Single Instruction, Multiple Threads)**:
  - 這是 NVIDIA GPU 的核心執行模型，精神上類似於 SIMD (Single Instruction, Multiple Data)。
  - 硬體會將 Block 內的 Thread 分組成大小為 32 的 **Warp**。
  - **一個 Warp 內的所有 32 個 Thread 在同一時間點執行完全相同的指令**。
  - **分支分歧 (Branch Divergence)**: 如果一個 Warp 內的 Thread 執行了條件分支 (if-else) 且走向了不同的路徑，硬體會**序列化**這兩個路徑。也就是說，先執行 `if` 路徑 (此時 `else` 路徑的 Thread 會閒置)，再執行 `else` 路徑 (此時 `if` 路徑的 Thread 閒置)。這會降低效能，應盡量避免。
  - **延遲隱藏 (Latency Hiding)**: SM 可以快速在不同的 Warp 之間切換。當一個 Warp 因為等待記憶體讀取而停頓時，硬體會立刻切換到另一個準備好的 Warp 執行，從而隱藏了記憶體延遲，維持計算單元的忙碌。

---

## 5. 高效能程式設計模式：Tiling

這是一個最大化利用 Shared Memory 以減少 Global Memory 存取的經典技巧，以矩陣乘法為例：

**目標**: C = A x B

1.  **劃分任務**: 將輸出矩陣 C 劃分為多個小的 tile (區塊)，每個 Block 負責計算一個 tile。
2.  **載入資料到 Shared Memory**:
    - 每個 Thread 從 Global Memory 中讀取矩陣 A 的一個元素和矩陣 B 的一個元素，並將它們存入 `__shared__` 記憶體中。
    - 呼叫 `__syncthreads()` 確保整個 Block 的所有 Thread 都已完成資料載入。
3.  **在 Shared Memory 中計算**:
    - 每個 Thread 從 Shared Memory 中讀取所需數據，進行部分內積運算，並將結果累加在自己的 Register 中。
    - 因為 Shared Memory 速度遠快於 Global Memory，此階段的計算非常高效。
4.  **重複載入與計算**: 透過迴圈，載入計算下一個 tile 所需的 A 和 B 的子矩陣，並重複步驟 2 和 3，直到整個 row 和 column 的內積完成。
5.  **寫回結果**: 所有計算完成後，每個 Thread 將其 Register 中最終的結果寫回 Global Memory 中對應的 C 矩陣位置。

這個模式大幅提升了**資料重用性 (Data Locality)**，顯著提高了 **Compute-to-Global-Memory-Access (CGMA) Ratio**，是 CUDA 優化的核心思想。