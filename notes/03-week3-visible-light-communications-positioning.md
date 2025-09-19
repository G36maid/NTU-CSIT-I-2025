# Week 3 - Visible Light Communications and Positioning

**授課教師**: 蔡欣穆教授  
**日期**: 2025/9/19  
**主題**: Visible Light Communications + Positioning

## 課程安排

- **W3 講課**: Visible Light Communications + Positioning
- **W4 講課**: Use AI to Solve Real-World Problems — Applications in Education and Aviation
- **成績計算**: COOL 線上考試題目，約 25 題（選擇題或簡答題），W4 上課後發布

## 授課教師介紹

**蔡欣穆教授**
- 國立臺灣大學資訊工程系 教授
- 教務處 副教務長 - 主管教務處資訊/數位化業務 + 教學發展/數位學習/學習規劃
- 前數位學習中心教學科技組組長 (2016—2023)
- 台大線上教學平台 NTU COOL 創設人 & 開發/維運團隊負責人
- 台大教學傑出獎、校內服務傑出獎、臺大學術勵進青年講座
- 2013年成為首位獲得「美國英特爾青年教師學術獎」之亞太區教師
- 曾任 General Motors R&D and Planning Intern Researcher

## 研究領域

- Visible Light Communications & Positioning
- Internet of Vehicles
- Digital Learning

## Part I: Visible Light Communications 概述

### VLC 簡介

**靈感來源**: 2011年 Prof. Harald Haas 的 TED Talk
- 展示了可見光通訊的概念和潛力
- 📺 [TED Talk: Wireless data from every light bulb](https://www.youtube.com/watch?v=NaoSp4NpkGg)

### 頻譜短缺問題

```
Radio Spectrum: 300 GHz - 40 GHz
Visible Light:  400 THz - 790 THz
```

**可見光頻譜優勢**:
- 免費且無需授權（目前）
- 頻寬遠大於無線電頻譜

### 系統組成

**發射器**: LED
- 頻寬: 數 MHz

**接收器選擇**:
1. **Photodiode (PD)**
   - 頻寬: 數 MHz
   - 像素: 1 個
   - 額外成本: 有

2. **Camera**
   - 頻寬: 30 frame/s → 30 Hz
   - 像素: 數百萬個
   - 額外成本: 最小

### 市場概況

**高亮度 LED 全球市場**:
- 2015年: 150 億美元
- 2023年預估: 220 億美元
- 全球 LED 晶片供應估計比全球需求多 15-18%
- 📊 [市場分析報告](https://www.gminsights.com/industry-analysis/high-brightness-HB-LED-market?gclid=Cj0KCQiAiNnuBRD3ARIsAM8KmluIFnSRo3orYHc4PgrcN3Nsu-YO_nkGE_2gW97f_IVnxfJ_TjmrRDsaAlBHEALw_wcB)

### 調變技術

**強度調變 (Intensity Modulation)**:
- 調變「功率」，而非「振幅」
- 無負值（無負「亮度」）
- 訊噪比公式: SNR = (r²P²) / (σ²shot + σ²thermal)
- 有效路徑損失指數是光學路徑損失指數的平方

### 通道特性

**獨特特性**:
- 通常為「視線傳播 (Line-of-Sight)」
- 較少干擾
- 較不嚴重的多重路徑衰減
- 方向性傳輸 + 有限接收視野 + 較高路徑損失指數

**功率接收公式**:
```
PR = ((n+1)AR)/(2πD²) × cos^n(φ) × cos(θ) × PT
```

### 商業考量

**設計原則**:
- 不要重複現有技術（如 WiFi）能做得很好的事
- WiFi 7 可達 2.35 Gbps（3m, 320 MHz, 實際測試）
- 需要利用 VLC 的獨特特性
- 🔗 [WiFi 7 速度測試討論](https://www.reddit.com/r/Ubiquiti/comments/1abp6as/u7pro_wiﬁ_7_speed_test_reaches_24gbps_from_phone/)

**生態系統複雜性**:
- 誰支付調變光源（TX）的成本？
- 誰支付修改行動裝置（RX）的成本？
- 投資報酬率如何？

## Part II: 車聯網與可見光通訊

### 自動駕駛分級

**SAE J3016 標準**:

| Level | 名稱 | 人類駕駛員任務 |
|-------|------|---------------|
| 0 | No Automation | 處理所有駕駛任務 |
| 1 | Driver Assistance | 處理加速減速或轉向其中之一 |
| 2 | Partial Automation | 車輛處理加速減速和轉向，人類監控環境 |
| 3 | Conditional Automation | 可閱讀、上網，但需要在短時間內接手 |
| 4 | High Automation | 可睡覺（無需接手，系統可轉換到低風險狀態）|
| 5 | Full Automation | 不需要人類駕駛員 |

### 聯網車輛 vs 自動駕駛車輛

**自動駕駛車輛**: 單一車輛做決策
**聯網車輛**: 協作、連接的車聯網

**合作的重要性**:
- 即使自動駕駛車輛也需要協調以獲得更好的「性能」
- 人類駕駛汽車、機車和行人需要更多協調
- 很長一段時間內，我們將有混合的人類駕駛和自動駕駛交通

### 車聯網的效益

#### 1. 增加道路容量

**問題**: 道路建設沒有摩爾定律
- 五股-楊梅高架橋（2013年完工）：58.7 億台幣建造 38.4 公里高架道路
- 🖼️ [五股楊梅高架橋照片](http://twimg.edgesuite.net//images/twapple/640pix/20121217/LN06/LN06_004.jpg)

**解決方案**: 車隊控制
- 在時速 100 公里時，道路表面利用率 = 1/20 ~ 5%
- 車隊可讓車道容量增加一倍（200%）或三倍（300%）
- 10 車車隊：6000-8000 輛/小時（相對於 2200 輛/小時）

#### 2. 降低能源消耗

**空氣動力學**:
- 高速公路上 50% 的能耗用於克服空氣阻力
- 緊密編隊的自動車隊可節省 10-20% 的總能耗
- 📚 [California PATH 自動化高速公路系統示範](http://www.path.berkeley.edu/publications/national-automated-highway-systems-consortium)

**加減速循環**: 浪費能源

#### 3. 改善安全性

**BMW iVISION Dee 智慧擋風玻璃範例**:
- 📺 [BMW iVISION Dee at CES 2023](https://www.youtube.com/watch?v=kNzQXqp-aSM)

**資訊分享的好處**:
- 擴展範圍：1 跳（感測）→ 2+ 跳（感測 + 通訊）
- 克服障礙物
- 減少長距離的不確定性
- 降低成本：有足夠市場滲透率時可能不需要所有感測器

**AVR (Augmented Vehicular Reality)**:
- 📺 [AVR: Augmented Vehicle Reality 演示](https://www.youtube.com/watch?v=9rOtH3hDcw8)

### 車用可見光通訊 (V2LC)

#### 系統架構

**發射器**: LED 尾燈
- 頻寬: MHz
- 人眼無法看到傳輸（> 100 Hz）因視覺暫留

**接收器**: 光電二極體或攝影機

#### 應用案例

**1. 交通震波緩解**
- LED 尾燈廣播移動資訊
- 光電二極體接收資訊
- 基於速度差異的即時速度建議
- 反應延遲減少高達 70%
- 📺 [交通震波現象演示](https://youtu.be/Mh6PNQbKBYo)

**2. 機車安全**
- 尾燈傳輸車輛當前速度、位置、方向和剎車/轉向燈狀態
- 鄰近車輛接收狀態資訊並在必要時警告駕駛
- 📰 [Intel 報導](http://www.engadget.com/2013/06/25/intel-talking-tail-lights/)

#### VLC 的優勢

**可靠通道**:
- 主要為視線傳播
- 穩定通道：微不足道的多重路徑衰減（相干時間少 10 倍！）
- 專用通道：通常同頻干擾極少（無存取協定/延遲！）

**市場推廣**:
- 潛在快速市場啟動
- 現有 TX：LED 照明
- 現有 RX：攝影機

**高頻寬**:
- 1 或 10 Gbps，150 m（專用硬體：雷射 LED、光電二極體接收器）
- 🔗 [KORUZA 光通訊系統](http://www.koruza.net)

#### VLC 的限制

- 不總是有效 - 主要為視線傳播
- 有障礙物時通常無效
- 天氣（雨、雪、霧）減少範圍
- 需要與射頻連結配合使用
- 連結傳輸率隨距離快速下降
- 強度調變/直接檢測（IM/DD）：距離增加 2 倍，訊號功率減少 1/16

## RayTrack 技術

### 問題與解決方案

**寬視野 vs 窄視野的缺點**:

**寬視野**:
- 優點：不易移出視野
- 缺點：陽光或其他環境干擾導致 SNR 降低

**窄視野**:
- 優點：較少環境干擾
- 缺點：容易移出視野導致通訊中斷

### RayTrack 動態視野調整

**核心技術**: Digital Micromirror Device (DMD)
- 快速切換（~kHz）ON/OFF 狀態重新導向入射光
- ON 狀態：+12°
- OFF 狀態：-12°

### 系統流程

1. **檢測階段**: 使用寬視野進行檢測
   - 透過在通訊頻率下找到高接收功率的位置來定位 TX

2. **通訊階段**: 使用窄視野進行通訊
   - 只開啟 TX 所在位置的像素
   - 關閉所有其他像素

### 優勢

- **無干擾**: 有效濾除環境光干擾
- **點對點連結**: 專用頻寬
- **無存取控制**: 無存取延遲

### 快速檢測

**互補壓縮感測**:
- ON-state photodiode: y+ = Φ+x + NON
- OFF-state photodiode: y− = Φ−x + NOFF
- 結果: y+ − y− = Φx

### 實驗結果

**高速公路測試**:
- 測試速度：70-100 km/hr
- 使用 LiDAR 和攝影機作為真值參考

## RayGen: 目標追踪車用 VLC 發射器

### 窄光束 TX 的優勢

- 更好的安全性（避免竊聽）✓
- 減少同頻干擾 ✓
- 集中功率 ✓
- 距離衰減較慢
- 減少背景雜訊影響
- 較少功耗 ✓

### 標記追踪系統

**接收器**:
- 不需要主動光源作為標記
- 無雜訊
- 使用反射或逆反射材料

**發射器**:
- 檢測圖案
- 即時將光束導向標記

### 技術實現

**光束分配器**:
- 只捕獲從標記反射回來的光
- 結合視野

**Galvo 鏡**:
- 動態調整攝影機視野和雷射光束

### 標記檢測

**深度學習模型**:
- 訓練集（80%）+ 測試集（20%）
- 1934 個正標記標籤
- 158 個含雜訊的負標籤圖像

**結果**:
- Precision: 99.7%
- Recall: 98.9%
- mAP50-95: 95.7%
- 時間性能: 100 FPS 以上

### 傳輸性能

**實際場景映射**:
- 在 5-20 m 距離，1.4 m/s 橫向速度下達到 50 Mbps
- 在 5-10 m 距離，0.5 m/s 橫向速度下達到 100 Mbps

## 總結

### 車輛協作的機會

- 增加道路容量
- 降低能源消耗
- 更安全的道路環境

### 可見光通訊的貢獻

- 透過可靠通訊實現協調至關重要
- 可見光通訊有助於緩解許多現有問題

### 相關資源連結

**學術論文**:
- Shao-Chi Chen et al., "Making in-Front-of Cars Transparent: Sharing First-Person-Views via Dashcam," Computer Graphics Forum (Proceedings of Pacific Graphics), 2014
- S.-H. You et al., "Demo: Visible Light Communications for Scooter Safety," ACM MobiSys, Taipei, Taiwan, June 2013
- Wen-Hsuan Shen and Hsin-Mu Tsai, "RayTrack: enabling interference-free outdoor mobile VLC with dynamic field-of-view," MobiSys '21

**參考網站**:
- [Wikimedia - Waymo](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cf/Waymo_self-driving_car_front_view.gk.jpg/1024px-Waymo_self-driving_car_front_view.gk.jpg)
- [New Atlas - SARTRE](https://newatlas.com/sartre-autonomous-road-train-completed/24170/)
- [Pixabay - 交通堵塞](https://pixabay.com/en/trafﬁc-jam-trafﬁc-highway-freeway-1703575/)
- [Doha News - 道路安全統計](https://dohanews.co/qatar-roads-are-getting-safer-according-to-statistics/)
- [SAE International](https://www.sae.org/autodrive)

### 詳細資源文件

**深度閱讀資料**:
- 📄 [SARTRE Autonomous Road Train Project](resources/sartre-autonomous-road-train.md) - 詳細的 SARTRE 專案分析
- 📄 [Berkeley PATH NAHSC](resources/berkeley-path-nahsc.md) - 美國自動化高速公路系統聯盟歷史

**YouTube 影片資源**:

#### VLC 技術基礎
- 📺 **Harald Haas TED Talk (2011)**: [Wireless data from every light bulb](https://www.youtube.com/watch?v=NaoSp4NpkGg)
  - VLC 概念的經典介紹
  - 展示 LED 照明的資料傳輸潛力
  - 可見光通訊的未來願景

#### 車用安全技術
- 📺 **BMW iVISION Dee (CES 2023)**: [Smart windshield technology](https://www.youtube.com/watch?v=kNzQXqp-aSM)
  - 智慧擋風玻璃展示
  - 擴增實境車用介面
  - 未來車用顯示技術

- 📺 **AVR Demo**: [Augmented Vehicle Reality](https://www.youtube.com/watch?v=9rOtH3hDcw8)
  - 擴增車用現實技術
  - 透視車輛技術演示
  - 安全性提升應用

#### 交通現象演示
- 📺 **Traffic Shockwave**: [實際交通震波現象](https://youtu.be/Mh6PNQbKBYo)
  - 交通震波的視覺化演示
  - 了解交通擁塞成因
  - VLC 解決方案的重要性

**歷史資料**:
- 📚 [California PATH Demo '97](http://www.path.berkeley.edu/publications/national-automated-highway-systems-consortium) - 1997年自動駕駛歷史性演示
- 📰 [Intel 機車安全報導](http://www.engadget.com/2013/06/25/intel-talking-tail-lights/) - 早期 VLC 應用案例

### 下週預告

Part III: Indoor Positioning with Light!

## 聯絡資訊
- **Email**: [hsinmu@csie.ntu.edu.tw](mailto:hsinmu@csie.ntu.edu.tw)