# 📘 Location Update Analysis — GitHub Block Math Version

This is a GitHub-friendly version of *Location Update Analysis*.  
All math formulas use KaTeX block syntax (`$$...$$`) for correct rendering.

---

# 1. What is Location Update (LU)?

In GSM/GPRS systems, the network tracks a phone’s **Location Area (LA)**, not the exact cell.  
Whenever an MS (mobile station) crosses into a new LA, it performs a **Location Update (LU)**.

### LU Trigger Conditions
1. Crosses LA boundary  
2. Periodic update  
3. IMSI attach (power-on registration)

---

# 2. Location Area (LA)

- Each LA contains multiple cells.  
- Each LA has a unique **LAI (Location Area Identity)**.  
- A cell broadcasts its LAI; when the MS detects a change → it performs LU.

### LA Size Trade-off

| LA too small | LA too large |
|---------------|--------------|
| Frequent LU → heavy signaling | Large paging area → high paging cost |

Goal → **minimize total Update + Paging cost**

---

# 3. Types of Location Update

| LU Type | Description |
|----------|-------------|
| **Intra-VLR** | Within same VLR (lowest cost) |
| **Inter-VLR (TMSI)** | Between VLRs, TMSI available |
| **Inter-VLR (IMSI)** | Between VLRs, no TMSI → highest cost |

---

# 4. LU Arrival Rate λ

$$
\lambda = \frac{v \rho L}{\pi}
$$

| Symbol | Meaning |
|---------|----------|
| \(v\) | user speed (km/h) |
| \(ρ\) | user density (MS/km²) |
| \(L\) | LA border length (km) |
| \(π\) | averaging factor |

---

# 5. LA Border Length L

$$
L = 6R\!\left(\frac{1}{3} + \frac{1}{2}\sqrt{3N - 3}\right)
$$

| Symbol | Meaning |
|---------|----------|
| \(R\) | cell radius |
| \(N\) | number of cells per LA |

---

# 6. Outer-ring Cells \(N_p\)

$$
N_p = 6\sqrt{\frac{N}{3}} - 3
$$

Only outer-ring cells generate cross-LA updates.

---

# 7. Radio Resource Occupancy

For a cell \(j\):

$$
Tr_{LU}^{(j)} = \lambda_{LU}^{(j)}
\left[
 \sum_{i=1}^{3} P^{(i,j)}_{LU}\, t^{(i)}_{LU}
\right]
$$

| Symbol | Meaning |
|---------|----------|
| \(λ_{LU}^{(j)}\) | LU arrival rate for cell j |
| \(P^{(i,j)}\) | probability of LU type i |
| \(t^{(i)}\) | duration (s) of LU type i |

---

# 8. MSC/VLR Database Load

$$
TTN_{LU} =
\sum_{j=1}^{N_{LA}N_p}
\lambda_{LU}^{(j)}
\!\left[
 \sum_{i=1}^{3}
 P^{(i,j)}_{LU} TN^{(i)}_{LU}
\right]
$$

| LU Type | DB Transactions |
|----------|----------------|
| Intra-VLR | 2 |
| Inter-VLR (TMSI) | 14 |
| Inter-VLR (IMSI) | 16 |

---

# 9. Example (PDF pp. 10-18)

### Parameters
- Density = 10 000 MS/km²  
- Cell radius = 0.5 km  
- Speed = 10 km/h  
- Cells per LA = 10  
- 5 LAs per MSC/VLR  

---

# 10. Case 1 – All Intra-VLR

$$
L = 1.377~\mathrm{km}
$$

$$
\lambda = 43831~\mathrm{LUs/h}
$$

$$
Tr = 43831\times\frac{0.6}{3600} = 7.3~\mathrm{Erlangs}
$$

At 1 % blocking → about 14 channels ≈ 2 TCH capacity.

---

# 11. Case 2 – All Inter-VLR (80 % TMSI, 20 % IMSI)

$$
E[t] = 0.8(3.5) + 0.2(4.0) = 3.6~\mathrm{s}
$$

$$
Tr = 43.83~\mathrm{Erlangs}
$$

≈ 57 channels → ≈ 8 TCH capacity  
Case 2 ≈ 6× the load of Case 1.

---

# 12. MSC/VLR Total Load (across 5 LAs)

$$
N_p = 6\sqrt{\frac{10}{3}} - 3 \approx 8
$$

| Type | Cells |
|-------|--------|
| Intra-VLR | \(3N_p = 24\) |
| Inter-VLR | \(2N_p = 16\) |

$$
TTN = 43831(48 + 230.4) = 1.22\times10^7
$$

≈ 12.2 million DB transactions / hour.

---

# 13. Key Findings

- Most LUs occur in outer-ring cells.  
- Inter-VLR (IMSI) updates dominate network cost.  
- Small LAs → heavy LU load; large LAs → heavy paging load.  
- Optimum LA size minimizes (LU + Paging) total cost.

---

# 14. Exam Highlights (必背)

1. \( \text{LU rate} = \tfrac{vρL}{π} \)  
2. Only outer-ring cells generate LUs.  
3. IMSI LUs are the most expensive.  
4. Case 2 ≈ 6 × Case 1 load.  
5. \( TTN ≈ 1.22\times10^7 \) DB ops per hour.

---

✅ **All formulas use block math `$$...$$` → fully renderable on GitHub KaTeX.**
