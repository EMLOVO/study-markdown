# 📘 Location Update Analysis — 全章節超完整總結（可編輯版）

本文件為 *location update analysis.pdf*（共 18 頁）的完整講解版。所有公式、例子、Case 1／Case 2、MSC/VLR 計算、無線負荷分析都包含在內。格式清楚、易讀，可直接用於期末考或速讀。

---

# **1. Location Update 的目的**

手機走動時，網路不需要知道手機在哪個 **cell**，只需要知道在哪個 **Location Area（LA）**。當手機跨過 LA 邊界，就必須做 Location Update（LU）。

LU 的目的：

* 讓網路在有人呼叫你時，能在正確的 LA 進行 paging
* 降低 tracking 成本（不必追蹤 cell-level）

LU 主要由以下情況觸發：

1. **跨 LA 邊界（最主要）**
2. **Periodic Update（週期性）**
3. **IMSI Attach（開機）**

---

# **2. Location Area（LA）定義**

* 一個 LA 包含許多 cell
* 每個 LA 有唯一 LAI（Location Area Identity）
* 每個 cell 廣播 LAI
* MS 發現 LAI 改變 → 觸發 Location Update

LA 太小 → 太頻繁 update → 網路超忙
LA 太大 → paging 範圍變大 → paging 成本很高

LA 大小是一個 tradeoff。

---

# **3. 三種 Location Update 類型（依成本從低到高）**

| 類型                         | 說明                    |
| -------------------------- | --------------------- |
| 1️⃣ **Intra-VLR LU**       | 在同一個 VLR 底下跨 LA → 最便宜 |
| 2️⃣ **Inter-VLR LU（TMSI）** | 跨 VLR，但仍有 TMSI        |
| 3️⃣ **Inter-VLR LU（IMSI）** | 跨 VLR 且沒有 TMSI → 成本最高 |

這三種類型的成本差異非常大，是後續分析的核心。

---

# **4. Location Update Rate λ（跨 LA 的頻率）**

LU 主要來自跨 LA 邊界。LU 到達率（每小時）：

[
\lambda = \frac{v \rho L}{\pi}
]

| 符號    | 意義                             |
| ----- | ------------------------------ |
| **v** | MS 平均移動速度（km/hr）               |
| **ρ** | 使用者密度（MS/km²）                  |
| **L** | LA 的 exposed perimeter（外圍邊界長度） |
| **π** | 平均化常數（MS 移動方向均勻）               |

越多人（ρ）、越快（v）、邊界越長（L） → LU 越多。

---

# **5. LA Exposed Perimeter L（外圍邊界）**

PDF 使用六角形蜂巣模型計算：

[
L = 6R \left(\frac{1}{3} + \frac{1}{2}\sqrt{3N - 3}\right)
]

| 參數    | 意義            |
| ----- | ------------- |
| **R** | cell 半徑       |
| **N** | LA 中 cell 的數量 |

更多 cell（N ↑） → LA 越大 → L 越大。

---

# **6. LA 外圈 cell 數量 Np**

[
N_p = 6\sqrt{\frac{N}{3}} - 3
]

Np = LA 的外圈 cell 數量。
只有外圈 cell 會產生 LU，內圈 cell 不會跨 LA。

---

# **7. 無線資源占用公式（SDCCH/SACCH 負載）**

對於某 cell j：

[
Tr_{LU}^{(j)} = \lambda_{LU}^{(j)} \left[ \sum_{i=1}^{3} P^{(i,j)}*{LU}\ t^{(i)}*{LU} \right]
]

| 符號     | 意義                          |
| ------ | --------------------------- |
| λ      | 此 cell 的 LU 到達率             |
| P(i,j) | 類型 i 的 LU 在 cell j 的比例      |
| t(i)   | 類型 i 的 LU 所需的無線時間（秒）        |
| Tr     | cell j 所需的 SDCCH 時間（Erlang） |

👉 一句話：

> **每個 cell 的負荷 = LU 次數 × 每次 LU 要用幾秒**

---

# **8. MSC/VLR Transaction Load（核心網路負荷）**

MSC/VLR 處理 LU 時需要做多次 database transaction。

公式：

[
TTN_{LU} = \sum_{j=1}^{N_{LA}N_p} \lambda_{LU}^{(j)} \left[ \sum_{i=1}^{3} P^{(i,j)}*{LU}\ TN^{(i)}*{LU} \right]
]

其中：

* TN(i) = 類型 i 的 LU 需要多少 DB 工作
* λ_LU^(j) = cell j 的 LU 到達率

類型成本（PDF 提供）：

| LU 類型            | DB 次數 | 無線時間  |
| ---------------- | ----- | ----- |
| Intra-VLR        | 2     | 0.6 s |
| Inter-VLR (TMSI) | 14    | 3.5 s |
| Inter-VLR (IMSI) | 16    | 4.0 s |

IMSI > TMSI > Intra
（IMSI 最貴）

---

# **9. Example（PDF 頁 10–18）— 完整解析**

Example 假設：

* 密度：10,000 MS/km²
* R = 500 m（0.5 km）
* v = 10 km/hr
* LA 有 N = 10 個 cell
* 一個 MSC 管 N_LA = 5 個 LA

會計算：

1. **單一邊界 cell 的無線負荷（Case 1 / Case 2）**
2. **整個 MSC/VLR 的 total DB transaction load**

下面依 PDF 分兩個 Case：

---

# **10. Case 1：所有 LU 都是 Intra-VLR（樂觀情況）**

### 10.1 計算 LA 的 L

[
L = 1.377 \text{ km}
]
（PDF 明確給出）

### 10.2 計算該 cell 的 LU 到達率

[
\lambda_{LU}^{(j)} = 43,831\ \text{LUs/hour}
]

### 10.3 計算無線負荷

[
Tr = 43,831 \times \frac{0.6}{3600} = 7.3 \text{ Erlangs}
]

### 10.4 所需 channel @ 1% blocking

PDF 結論：

> **≈ 14 channels → 約 2 條 TCH 容量**

---

# **11. Case 2：所有 LU 都是 Inter-VLR（80% TMSI, 20% IMSI）**

### 11.1 LU 到達率不變

[
\lambda_{LU}^{(j)} = 43,831
]

### 11.2 計算平均 LU 時間

[
E[t] = 0.8(3.5) + 0.2(4.0) = 3.6\ \text{秒}
]

### 11.3 無線負荷

[
Tr = 43.83 \text{ Erlangs}
]

### 11.4 所需 channel

PDF 結論：

> **≈ 57 channels → 約 8 條 TCH 容量**

Case 2 是 Case 1 的 **6 倍負荷**。

---

# **12. MSC/VLR Total Transaction Load（PDF 頁 16–18）**

在整個 MSC 底下有：

* 5 個 LA
* 每個 LA 外圈 cell ≈ Np

Np 公式：
[
N_p = 6\sqrt{\frac{10}{3}} - 3 ≈ 7.95 ≈ 8
]

### 12.1 分類 cell 數：

**Intra-VLR cell：**
[
N_p + 4/2N_p = 3N_p = 24
]

**Inter-VLR cell：**
[
4/2N_p = 16
]

### 12.2 計算 MSC/VLR DB Load

總 DB 工作：

[
TTN = \lambda_{LU}[2(24) + 16(0.8\cdot14 + 0.2\cdot16)]
]

整理：
[
TTN = 43,831[48 + 230.4] = 1.22 \times 10^7
]

PDF 最終結論：

> **MSC/VLR 在尖峰一小時要做約 1,220 萬次 DB transaction**

---

# **13. 核心結論（PDF 最後頁）**

* 大多數 LU來自 **LA 外圈 cell**
* **Inter-VLR（尤其 IMSI）成本極高**
* If LA 太小 → Update cost 高（無線 & MSC 都爆）
* If LA 太大 → Paging cost 高
* LA 設計目標：**讓 Update + Paging 兩者成本最小化**

---

# **14. 考試金句（背熟即可拿分）**

1️⃣ Location Update Rate = 使用者密度 × 速度 × 邊界長度 ÷ π
2️⃣ LA only tracks area, not cell → paging 在整個 LA 進行
3️⃣ Intra-VLR < TMSI < IMSI（成本從低到高）
4️⃣ Case 2（Inter-VLR）負荷 ≈ Case 1 的 6 倍
5
