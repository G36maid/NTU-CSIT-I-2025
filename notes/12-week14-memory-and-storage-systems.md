# Week 14: Memory and Storage Systems

- **授課教師**: 張原豪 (Yuan-Hao Chang)
- **主題**: An in-depth exploration of memory and storage systems, from their historical evolution and modern implementations to next-generation technologies and the data structures that power them.

本週課程由張原豪教授介紹，涵蓋了從古至今的儲存媒體演進、當代主流儲存技術 (HDD, SSD) 的內部原理與挑戰、次世代記憶體 (NVMs) 與記憶體內運算 (PIM) 的興起，以及針對這些新硬體所設計的資料索引結構。

---

## 1. 儲存媒體的歷史演進

- **古代儲存**: 從莎草紙 (Papyrus)、羅賽塔石碑 (Rosetta Stone)，到中國的竹簡，儲存密度極低。紙的發明是早期一大躍進。
- **現代儲存**:
    - **1950s-1980s**: 磁性儲存興起，從巨大的 IBM RAMAC (5MB) 到軟碟 (Floppy Disk)。
    - **1980s-2000s**: 硬碟 (HDD) 和光碟 (CD) 成為主流，固態硬碟 (SSD) 概念出現。
    - **2000s-Today**: 雲端儲存 (Cloud Storage) 普及，HDD 容量突破 TB 級，3D NAND Flash 技術使 SSD 容量和成本效益大增。
- **永久儲存挑戰**: 現代基於 IC 的儲存裝置難以長期保存資料。GitHub 的「北極代碼庫」專案選擇使用**磁帶 (Magnetic Tape)** 來永久保存開源碼，因其成本低、壽命長、安全性高。

---

## 2. 當代主流儲存技術詳解

### 硬碟 (Hard Disk Drive, HDD)

- **原理**: 透過磁頭在高速旋轉的磁性碟盤上讀寫資料。
- **容量瓶頸**: 傳統的垂直磁性錄寫 (PMR) 技術因**超順磁效應 (Superparamagnetic Effect, SPE)** 而達到密度極限。當磁性顆粒過小時，熱擾動會使其磁性方向不穩定，導致資料遺失。
- **新一代磁記錄技術**:
    1.  **疊瓦式磁性錄寫 (Shingled Magnetic Recording, SMR)**:
        - **原理**: 寫入時，新的磁軌會部分覆蓋前一個磁軌，如同屋瓦般堆疊，從而提升磁軌密度（約 25%）。其利用了讀取頭比寫入頭更精密的特性。
        - **挑戰**: **無法原地更新 (In-place update)**。修改一個磁軌上的資料會破壞後續磁軌，因此所有寫入都必須是循序的。更新資料需要昂貴的**讀-改-寫 (Read-Modify-Write)** 操作，或透過 Flash Translation Layer (FTL) 進行管理。
    2.  **交錯式磁性錄寫 (Interlaced Magnetic Recording, IMR)**:
        - 將磁軌分為上下兩層，上層磁軌部分覆蓋下層，可再提升約 40% 密度。同樣面臨無法原地更新的問題。

### NAND Flash Memory (SSD)

Flash Memory 是當今行動裝置、PC 到企業儲存的核心。

- **基本單元**: **浮動閘極電晶體 (Floating-Gate Transistor)**。透過在浮動閘極中儲存或釋放電子，來改變電晶體的**閾值電壓 (Threshold Voltage, Vth)**，從而表示 0 或 1。
- **儲存單元類型**:
    - **SLC (Single-Level Cell)**: 2 個電壓狀態，儲存 1 bit。速度快、壽命長、成本高。
    - **MLC (Multi-Level Cell)**: 4 個電壓狀態，儲存 2 bits。
    - **TLC (Triple-Level Cell)**: 8 個電壓狀態，儲存 3 bits。密度越高，成本越低，但速度、壽命和可靠性也隨之降低。
- **架構演進**: 從 2D (平面) 走向 **3D NAND**，透過垂直堆疊多層記憶體單元來大幅提升密度，而非僅縮小單元尺寸。這也帶來了新的干擾問題（Z-direction Disturb）。
- **三大特性與挑戰**:
    1.  **先抹除後寫入 (Erase-before-write)**:
        - **操作單位**: 讀寫以 **Page** 為單位，抹除以 **Block** (由多個 Page 組成) 為單位。
        - **影響**: 無法原地更新。所有更新都必須寫入到新的 Page，舊 Page 標記為失效。這導致了**垃圾回收 (Garbage Collection, GC)** 的需求，需定期將有效資料搬移並抹除舊 Block。
    2.  **寫入壽命有限 (Limited Endurance)**:
        - 每次抹除操作都會對氧化層造成微小損傷，一個 Block 只能承受有限的抹除次數 (SLC 約 10 萬次，TLC 約數千次)。
        - **影響**: 需要**平均抹寫儲存 (Wear Leveling)** 演算法，確保所有 Block 的抹除次數盡可能均勻，以延長 SSD 整體壽命。
    3.  **可靠性問題**:
        - **讀寫干擾 (Read/Write Disturb)**: 對一個 Page 進行操作時，可能微弱地改變相鄰 Page 中的電子狀態，累積後會導致位元錯誤。
        - **資料保存錯誤 (Data Retention Errors)**: 隨著時間和抹寫次數增加，浮動閘極中的電子會逐漸流失，導致資料錯誤。
- **快閃記憶體轉換層 (Flash Translation Layer, FTL)**: 位於 SSD 控制器中的韌體，負責處理上述所有複雜問題 (位址轉換、GC、Wear Leveling、壞塊管理、ECC 錯誤更正)，讓上層作業系統能像對待一般硬碟一樣對其進行讀寫。

---

## 3. 次世代記憶體與記憶體內運算 (PIM)

### 新興非揮發性記憶體 (Non-Volatile Memories, NVMs)

這些技術旨在填補 DRAM (快、貴、揮發性) 與 NAND Flash (慢、便宜、非揮發性) 之間的鴻溝。
- **ReRAM (可變電阻式記憶體)**: 透過改變材料的電阻狀態 (高/低) 來儲存 0/1。
- **PCM (相變化記憶體)**: 透過加熱使材料在晶體和非晶體狀態間轉換，兩種狀態電阻不同。Intel Optane (3D XPoint) 即基於此技術。
- **MRAM (磁阻式隨機存取記憶體)**: 利用磁性穿隧結 (MTJ) 的磁阻變化來儲存資料，速度接近 SRAM。

### 記憶體內運算 (Processing-in-Memory, PIM)

- **馮紐曼瓶頸 (Von Neumann Bottleneck)**: 傳統架構中，CPU 和記憶體是分離的，大量資料在兩者間的來回搬運成為效能瓶頸，尤其在 AI 運算中。
- **PIM 概念**: 將部分運算功能直接移入記憶體中執行，減少資料搬運。
- **憶阻器交叉陣列 (Memristor Crossbar Array)**:
    - **架構**: ReRAM/PCM 等憶阻器單元排列成網格狀。
    - **原理**: 利用物理定律，當電壓 (輸入向量) 施加於 crossbar 的 wordlines 時，流經 bitlines 的電流總和 (I = V × G, G為電導) 自然地完成了**乘積累加 (Multiply-Accumulate, MAC)** 運算。
    - **應用**: 非常適合用來加速神經網路中的矩陣向量乘法。
- **內容可定址記憶體 (Content-Addressable Memory, CAM)**: 傳統 RAM 輸入位址、輸出資料；CAM 則輸入資料、輸出其位址，可實現高效的記憶體內搜尋。

---

## 4. 未來儲存技術

- **玻璃儲存 (Glass Storage)**:
    - **原理**: 使用飛秒雷射在石英玻璃內部蝕刻出微小的 3D 奈米結構 (Voxel)，透過改變其偏振角度和相位延遲來編碼多個位元。
    - **特性**: 極度耐久，可保存資料超過一萬年，適合 **WORM (Write-Once-Read-Many)** 的冷資料封存場景。
- **DNA 儲存**:
    - **原理**: 將二進位資料 (00, 01, 10, 11) 對應到 DNA 的四種鹼基 (A, C, G, T)，並合成對應的 DNA 分子。
    - **特性**: 儲存密度極高 (1 克 DNA 可存 215 PB)，保存時間長。
    - **挑戰**: 目前讀寫速度極慢，成本極高。

---

## 5. 現代儲存的資料索引結構

- **LSM-Tree (Log-Structured Merge-Tree)**:
    - **架構**: 由一個記憶體中的 C0 層 (通常是 skiplist 或 tree) 和多個磁碟上大小指數增長的 Ck 層 (SSTable) 組成。
    - **寫入**: 所有寫入都先進入記憶體中的 C0。當 C0 滿了，會與磁碟上的 C1 **合併排序 (Compaction)**，生成新的 C1。此過程逐層向下傳遞。
    - **優點**: 將隨機寫入轉換為**循序寫入**，非常適合 HDD 和寫入密集的場景。
    - **挑戰**:
        - **寫入放大 (Write Amplification)**: 一筆使用者資料可能因多次 Compaction 而被實際寫入磁碟數次。
        - **讀取放大 (Read Amplification)**: 查詢時可能需要從 C0 到 Ck 逐層尋找。
    - **優化 (WiscKey)**: **鍵值分離 (Key-Value Separation)**。LSM-Tree 中只儲存 Key 和 Value 的位址指標，而大的 Value 本體則循序寫入一個獨立的 Value Log。這大幅縮小了 LSM-Tree 的大小，顯著降低了 Compaction 造成的寫入放大。
- **Bε-tree**:
    - B+ Tree 的變種，專為寫入優化。
    - **原理**: 在樹的每個節點中加入一個**緩衝區 (buffer)**。插入、更新、刪除操作作為「訊息」先被放入根節點的 buffer 中。當 buffer 滿了，訊息才會被**批次**向下推送到子節點，有效減少了對底層儲存的寫入次數和樹結構的調整頻率。
- **雜湊 (Hashing)**:
    - 適用於**持久性記憶體 (Persistent Memory, PM)** 的雜湊結構需要特殊設計，以保證操作的**一致性**和**原子性**，並減少昂貴的 PM 寫入。Cuckoo Hashing、Level Hashing、CCEH 都是此方向的研究。
- **學習型索引 (Learned Index)**:
    - **概念**: 用一個訓練好的機器學習模型（如小型神經網路）來取代或輔助傳統的索引結構。
    - **原理**: 模型學習資料的**累積分佈函數 (CDF)**，對於給定的 Key，可以直接**預測**其在已排序資料中的大致位置。`position = model(key)`。
    - **優點**: 在資料分佈有規律可循時，可以比 B-Tree 或 Hash Table 實現更小的空間佔用和更快的查詢速度。