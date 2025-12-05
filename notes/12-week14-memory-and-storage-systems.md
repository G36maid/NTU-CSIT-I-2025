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
- **架構演進**:
    - **2D 到 3D NAND**: 從 2D (平面) 走向 **3D NAND**，透過垂直堆疊多層記憶體單元來大幅提升密度。
    - **浮動閘極 (FG) 到電荷擷取 (Charge-Trap, CT)**: 現代 3D NAND 主流採用 Charge-Trap 技術，它將電荷儲存在絕緣的氮化矽 (Si₃N₄) 層中，相比傳統的導電浮動閘極，能更好地防止電荷洩漏，減少單元之間的干擾。
- **三大特性與挑戰**:
    1.  **先抹除後寫入 (Erase-before-write)**:
        - **操作單位**: 讀寫以 **Page** 為單位，抹除以 **Block** (由多個 Page 組成) 為單位。
        - **影響**: 無法原地更新。所有更新都必須寫入到新的 Page，舊 Page 標記為失效。這導致了**垃圾回收 (Garbage Collection, GC)** 的需求，需定期將有效資料搬移並抹除舊 Block。
    2.  **寫入壽命有限 (Limited Endurance)**:
        - 每次抹除操作都會對氧化層造成微小損傷，一個 Block 只能承受有限的抹除次數 (SLC 約 10 萬次，TLC 約數千次)。
        - **影響**: 需要**平均抹寫儲存 (Wear Leveling)** 演算法，確保所有 Block 的抹除次數盡可能均勻，以延長 SSD 整體壽命。
    3.  **可靠性問題**:
        - **讀寫干擾 (Read/Write Disturb)**: 對一個 Page 進行操作時，可能微弱地改變相鄰 Page 中的電子狀態 (稱為 "Weak Programming" 效應)，累積後會導致位元錯誤。
        - **Z方向干擾 (Z-direction Disturb)**: 在 3D NAND 中，對某一層的儲存單元進行編程，不僅會干擾同層的鄰居，還會干擾**上下層**的單元。
        - **資料保存錯誤 (Data Retention Errors)**: 隨著時間和抹寫次數增加，浮動閘極中的電子會逐漸流失，導致資料錯誤。
- **快閃記憶體轉換層 (Flash Translation Layer, FTL)**: 位於 SSD 控制器中的韌體，負責處理上述所有複雜問題 (位址轉換、GC、Wear Leveling、壞塊管理、ECC 錯誤更正)，讓上層作業系統能像對待一般硬碟一樣對其進行讀寫。
- **進階 SSD 概念**:
    - **開放通道SSD (Open-Channel SSD, OCSSD)**: 將一部分 FTL 的管理功能 (如 I/O 調度、GC) 移交給主機 (Host) 執行，讓應用程式能根據自身需求進行更深度的效能優化，克服傳統 SSD 內部資源有限的瓶頸。
    - **分區命名空間SSD (Zoned Namespace, ZNS SSD)**: 將 SSD 空間劃分為多個 Zone，每個 Zone 內的寫入必須是循序的。這個限制簡化了 SSD 內部的管理，大幅減少了寫入放大和 GC 開銷，從而提升效能和壽命。

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

### 釋放記憶體內運算的潛力

PIM 不僅是理論概念，業界和學界已提出多種具體實現，專注於解決不同應用的瓶頸。

#### ReRAM-based In-Memory Computing

ReRAM Crossbar 在執行類比 MAC 運算時，面臨**類比變化誤差 (Analog Variation Error)** 的挑戰，這會影響計算精度和系統的可擴展性。相關研究致力於：
- **解決擴展性問題**: 透過**適應性資料操作設計 (Adaptive Data Manipulation)**，包含權重捨入 (Weight-rounding)、適應性輸入子循環 (Adaptive Input Subcycling) 等技術，可將類比誤差顯著降低，提升大規模陣列的計算準確度。
- **優化圖形處理**: 針對圖形資料的稀疏和不規則特性，透過**圖形感知資料放置 (Graph-aware data placement)** 等方法，將鄰接矩陣重新排序和分區，有效提升 ReRAM Crossbar 的空間利用率和運算強度 `[ICCAD’22]`。
- **加速決策樹與圖神經網路**:
    - **隨機森林 (Random Forest)**: 利用 **TCAM (三元內容可定址記憶體)** 的特性，在 ReRAM Crossbar 上實現並行的**範圍匹配 (Range Matching)**，可高效完成決策樹的推論過程 `[DAC’23]`。
    - **圖神經網路 (GNN)**: 結合 TCAM 進行鄰居節點的高效搜尋，並利用 ReRAM Crossbar 進行特徵聚合，顯著提升 GNN 在稀疏圖上的訓練和推論效能 `[IEEE TETC’24]`。

#### Flash-based In-Memory Computing

將運算能力下推至 NAND Flash 儲存層級，是另一個重要的 PIM 方向。
- **ICE (Intelligent Cognition Engine) `[MICRO’22]`**:
    - **目標**: 加速邊緣裝置上的**向量相似度搜尋 (Vector Similarity Search)**。
    - **方法**: 在現有 Flash 晶片 (如 eMMC) 中實現一個**數位化**的 PIM 加速器。透過特殊的**位元容錯資料編碼 (Bit-Error-Tolerance Data Encoding)** 和**分層 Top-N 搜尋**，無需昂貴的 ADC/DAC 轉換器，即可在 Flash 內部完成大量向量的距離計算和篩選。
    - **成果**: 相比傳統邊緣系統，執行時間和能效分別提升了 17-95倍 和 11-140倍。
- **SiM (Search-in-Memory) `[TCAD’24]`**:
    - **目標**: 解決資料庫索引查詢時的 I/O 瓶頸。
    - **方法**: 重複利用 Flash 內部現有的**頁面緩衝區 (Page Buffers)** 和電路，將 "查詢" 指令送入晶片內部，直接在緩衝區內進行資料比對，而非將整個資料頁讀出到主機。
    - **成果**: 為寫入密集型工作負載帶來高達 9倍 的速度提升，並因減少 I/O 而節省 45% 的能耗。

---

## 4. 賽道記憶體 (Racetrack Memory)

賽道記憶體被視為一種極具潛力的新興非揮發性記憶體，有望提供接近 SRAM 的讀寫效能和媲美 Flash 的儲存密度。

### 類型：Domain-Wall vs. Skyrmion

- **磁域壁賽道記憶體 (Domain-Wall Racetrack Memory, DW-RM)**:
    - **原理**: 在奈米級的磁性「賽道」上，利用自旋極化電流來移動磁域 (magnetic domain) 的邊界——即「磁域壁 (domain wall)」，透過偵測磁域壁的位置或序列來儲存資料。
    - **挑戰**: 需要較高的驅動電流，且磁域壁在移動過程中可能不穩定。
- **斯格明子賽道記憶體 (Skyrmion Racetrack Memory, SK-RM)**:
    - **原理**: 利用一種稱為「斯格明子 (Skyrmion)」的拓撲穩定磁性結構作為資料位元。它像一個磁場中的奈米級渦旋，非常穩定且只需要極低的電流就能驅動。
    - **優勢**: 相比 DW-RM，SK-RM 具有尺寸更小、穩定性更高、驅動電流密度極低等優點，被認為是更理想的方案。

### SK-RM 的挑戰與優化

雖然前景廣闊，但 SK-RM 仍面臨諸多挑戰：
- **位移耗時**: 主要的延遲來自於需要透過「位移 (shift)」操作將目標資料移動到固定的讀寫端口。
- **位置錯誤**: 在位移過程中，斯格明子 (代表 1) 和非斯格明子區域 (代表 0) 的移動速度可能不一致，導致「掉隊 (out-of-step)」或「卡在中間 (stop-in-middle)」的位置錯誤。
    - **解決方案**: 提出 **p-ECC (Position Error Correction Code)** 等編碼方案來偵測和校正這類錯誤。
- **資料表示錯誤**: 連續多個 0 可能難以被精確區分。
    - **解決方案**: 採用 **FGS (Fixed Guard Skyrmion)** 或 **GGS (Greedy Guard Skyrmion)** 等「位元填充 (bit-stuffing)」方法，在連續的 0 之間插入保護性的 1，以確保資料表示的正確性。
- **寫入不對稱**: 生成一個斯格明子 (寫入 1) 比位移 (產生 0) 需要更多的時間和能量。
    - **解決方案**: 提出 **Permutation-Write** 策略，盡可能「重用」賽道上已存在的斯格明子來組成新資料，而不是銷毀後重新生成，從而減少昂貴的注入操作。

### SK-RM 的應用

- **演算法重新設計**: 傳統為 DRAM 設計的隨機存取演算法在 SK-RM 上效率低下。需要重新設計「位移受限 (shift-limited)」的演算法，例如專為 SK-RM 設計的排序演算法，以最小化位移操作。
- **邏輯閘**: 利用斯格明子的交互作用，可以在賽道記憶體上直接構建 **AND/OR** 甚至 **Fredkin** 等邏輯閘，實現真正的「存算一體 (Processing-in-Memory)」。

---

## 5. 其他未來儲存技術

### 玻璃儲存 (Glass Storage)

- **原理**: 使用**飛秒雷射 (femtosecond laser)** 在石英玻璃內部蝕刻出微小的 3D 奈米結構 (Voxel)。每個 Voxel 的**偏振角度 (polarization angle)** 和 **相位延遲 (retardance)** 等光學特性都可以被調製，從而使單一 Voxel 能編碼多個位元。
- **系統架構**:
    - **寫入**: 由飛秒雷射系統和精密的 XYZ 軸機械移動平台構成，負責在玻璃中定位並寫入 Voxel。
    - **讀取**: 使用偏振光學顯微鏡，透過分析光線穿過 Voxel 後的偏振變化來解碼資料。
    - **讀取優化**: 讀取時，Z 軸的對焦速度 (光學調整) 遠快於 X/Y 軸的機械移動速度。因此，**Z-First (Z軸優先)** 的數據佈局和讀取策略可以顯著提升效能。
- **機器學習輔助讀取**: 傳統的閾值判斷法容易受雜訊干擾。採用 **CNN (卷積神經網路)** 模型可以直接學習 Voxel 的圖像模式與其對應位元值之間的關係，實現超過 99% 的解碼準確率。
- **效能優化策略**: 針對其機械移動特性，提出了如 **Zigzag (Z字形)** 數據放置、**SMTF (尋道移動時間優先)** 等調度演算法，以最小化讀寫頭的移動和加減速時間。
- **特性**: 極度耐久，可保存資料超過一萬年，適合 **WORM (Write-Once-Read-Many)** 的冷資料封存場景。

### DNA 儲存

- **原理**: 將二進位資料 (00, 01, 10, 11) 對應到 DNA 的四種鹼基 (A, C, G, T)，並透過 **DNA 合成 (synthesis)** 技術產生對應的 DNA 分子序列。讀取時，利用 **PCR** 技術選取目標片段，再透過 **DNA 測序 (sequencing)** 還原出原始資料。
- **特性**: 儲存密度極高 (1 克 DNA 可存 215 PB)，保存時間長。
- **挑戰**: 目前讀寫速度極慢，成本極高，且合成與測序過程容易出錯。

---

## 6. 現代儲存的資料索引結構

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
    - **挑戰**: 傳統雜湊演算法在調整大小時需要昂貴的「全局重雜湊 (Full-Table Rehashing)」。而當應用於**持久性記憶體 (Persistent Memory, PM)** 時，更面臨保證**一致性**和**原子性**的額外開銷 (需要 Cache Line Flush 和 Memory Fence 等指令)。
    - **Cuckoo Hashing**: 一種解決碰撞的方法，每個鍵值有兩個備選位置。當插入新鍵值時，如果兩個位置都有人佔用，它會像杜鵑鳥一樣「踢走」其中一個，被踢走的鍵值再去尋找它的另一個備選位置，此過程可能引發一連串的「踢走」行為。
    - **針對 PM 的優化研究**:
        - **Level Hashing `[OSDI’18]`**: 採用兩層結構 (Top Level, Bottom Level)，插入新資料時最多只觸發一次資料搬遷，並透過「原地增量調整大小 (In-Place Incremental Resizing)」技術，在擴容時僅對部分舊 bucket 進行重雜湊，大幅減少了 PM 的寫入量。
        - **CCEH (Cacheline-Conscious Extendible Hashing) `[FAST’19]`**: 基於「可擴展雜湊 (Extendible Hashing)」，引入中間層 Segment，實現了快取行友好的兩次記憶體存取查找。透過寫入時複製 (Copy-on-Write) 和懶惰刪除 (Lazy Deletion) 等機制實現了無鎖查找和低寫入開銷的分割。
        - **Dash `[PVLDB’20]`**: 透過「指紋 (Fingerprinting)」技術，在不讀取完整 Key 的情況下就能快速過濾掉不匹配的項目，極大地減少了 PM 的讀取。
        - **SEPH `[OSDI’23]`**: 一種混合式設計，結合了 Level Hashing 的低擴容開銷和 Segment 的高查詢效率，實現了高效能與高可預測性的平衡。
- **學習型索引 (Learned Index)**:
    - **概念**: 用一個訓練好的機器學習模型（如小型神經網路）來取代或輔助傳統的索引結構。
    - **原理**: 模型學習資料的**累積分佈函數 (CDF)**，對於給定的 Key，可以直接**預測**其在已排序資料中的大致位置。`position = model(key)`。
    - **優點**: 在資料分佈有規律可循時，可以比 B-Tree 或 Hash Table 實現更小的空間佔用和更快的查詢速度。