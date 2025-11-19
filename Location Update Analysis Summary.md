# 📘 Location Update Analysis — GitHub Markdown Version

This is a complete, clean, and GitHub-friendly Markdown summary of *Location Update Analysis*.

---

# 1. Location Update 的目的

手機移動時，網路不需要知道手機在哪個 cell，只需要知道在哪個 **Location Area (LA)**。  
只要跨過 LA → 必須做 **Location Update (LU)**。

### 觸發 LU 的三種情況
1. **跨 LA 邊界（主要原因）**
2. **Periodic Update（週期更新）**
3. **IMSI Attach（開機登入）**

---

# 2. Location Area (LA) 定義

- 一個 LA 包含多個 cell  
- 每個 LA 有唯一 **LAI (Location Area Identity)**  
- Cell 會廣播 LAI  
- MS 偵測 LAI 改變 → 觸發 Location Update

### LA 大小的取捨

| LA 太小 | LA 太大 |
|--------|---------|
| Update 太頻繁 → signaling 高 | Paging 範圍大 → paging cost 高 |

---

# 3. Location Update 類型（成本由低到高）

| 類型 | 說明 |
|------|------|
| **Intra-VLR LU** | 同 VLR 內跨 LA，最便宜 |
| **Inter-VLR LU (TMSI)** | 跨 VLR，有 TMSI |
| **Inter-VLR LU (IMSI)** | 跨 VLR，沒 TMSI → 最貴 |

---

# 4. Location Update Rate λ（跨 LA 頻率）

PDF 給的 LU 到達率：

\[
\lambda = \frac{v \rho L}{\pi}
\]

### 參數說明

| 符號 | 意義 |
|------|------|
| \(v\) | 移動速度 (km/hr) |
| \(ρ\) | 使用者密度 (MS/km²) |
| \(L\) | LA 外圍邊界長度 |
| \(π\) | 平均化因子 |

➡ 越多人、越快、邊界越長 → LU 越多。

---

# 5. LA Exposed Perimeter L（邊界長度）

蜂巢模型公式：

\[
L = 6R\left(\frac{1}{3} + \frac{1}{2}\sqrt{3N - 3}\right)
\]

| 參數 | 意義 |
|------|------|
| \(R\) | cell 半徑 |
| \(N\) | LA 裡 cell 數 |

---

# 6. LA 外圈 cell 數量 \(N_p\)

\[
N_p = 6\sqrt{\frac{N}{3}} - 3
\]

只有外圈 cell 會產生跨 LA 的 LU。

---

# 7. 無線資源占用（SDCCH/SACCH）

對 cell j：  
LU 造成的負荷：

\[
Tr_{LU}^{(j)} = \lambda_{LU}^{(j)} 
\left[
\sum_{i=1}^{3} P^{(i,j)}_{LU}\, t^{(i)}_{LU}
\right]
\]

| 符號 | 意義 |
|------|------|
| \(λ_{LU}^{(j)}\) | 該 cell 的 LU 到達率 |
| \(P^{(i,j)}\) | LU 類型比例 |
| \(t^{(i)}\) | LU 類型 i 的無線耗時 |

➡ **cell 無線負荷 = LU 次數 × 每次 LU 占用時間**

---

# 8. MSC/VLR Transaction Load（Database 負載）

MSC/VLR 每次 LU 要做多次 database 操作：

\[
TTN_{LU} = \sum_{j=1}^{N_{LA}N_p} 
\lambda_{LU}^{(j)}
\left[
\sum_{i=1}^{3} P^{(i,j)}_{LU} TN^{(i)}_{LU}
\right]
\]

PDF 給的 DB 操作次數：

| LU 類型 | DB 次數 |
|--------|---------|
| Intra-VLR | 2 |
| Inter-VLR (TMSI) | 14 |
| Inter-VLR (IMSI) | 16 |

---

# 9. Example（PDF 頁 10–18）

### 參數設定
- 密度：10,000 MS/km²  
- R = 0.5 km  
- v = 10 km/hr  
- 每 LA：10 個 cell  
- 每 MSC：5 個 LA  

計算內容：
1. **Case 1：全部 Intra-VLR LU**
2. **Case 2：全部 Inter-VLR LU**
3. **整個 MSC 的 DB Load**

---

# 10. Case 1：全部 Intra-VLR（樂觀情況）

### 10.1 L（外圍長度）

\[
L = 1.377~\text{km}
\]

### 10.2 LU 到達率

\[
\lambda = 43{,}831\ \text{LUs/hour}
\]

### 10.3 無線負荷

\[
Tr = 43{,}831 \times \frac{0.6}{3600}
= 7.3~\text{Erlangs}
\]

### 10.4 所需 channel
- ~14 channels（1% blocking）  
- ≈ 2 條 TCH 容量

---

# 11. Case 2：全部 Inter-VLR（80% TMSI, 20% IMSI）

### 11.1 平均 LU 時間

\[
E[t] = 0.8(3.5) + 0.2(4.0) = 3.6~\text{s}
\]

### 11.2 無線負荷

\[
Tr = 43.83~\text{Erlangs}
\]

### 11.3 所需 channel
- ~57 channels  
- ≈ 8 條 TCH 容量  

➡ **Case 2 = Case 1 的約 6 倍負荷**

---

# 12. MSC/VLR 總 DB Load（跨 5 個 LA）

### 12.1 外圈 cell 數

\[
N_p = 6\sqrt{\frac{10}{3}} - 3 \approx 8
\]

### 12.2 cell 類型統計

| 類型 | cell 數 |
|------|---------|
| Intra-VLR | \(3N_p = 24\) |
| Inter-VLR | \(2N_p = 16\) |

### 12.3 每小時 DB 工作量

\[
TTN = 43{,}831[48 + 230.4] 
= 1.22 \times 10^7
\]

➡ **尖峰時段 ~ 1,220 萬次 DB 操作**

---

# 13. 結論（PDF）

- LU 大多發生在外圈 cell  
- Inter-VLR（尤其 IMSI）成本最高  
- LA 太小 → Update cost 高  
- LA 太大 → Paging cost 高  
- **最佳 LA 大小 = Update + Paging 成本最小化**

---

# 14. 必背金句（考試必用）

1. LU rate = \(v ρ L / π\)  
2. 外圈 cell 才會產生 LU  
3. IMSI LU 成本最高  
4. Case 2 ≈ Case 1 的 6 倍負荷  
5. MSC/VLR 一小時 ≈ \(1.22\times10^7\) DB ops

