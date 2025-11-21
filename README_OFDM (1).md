---
layout: default
title: OFDM Technical Summary
---

<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

# OFDM Technical Summary  
(Orthogonal Frequency Division Multiplexing — 教科書級完整版)

This document provides a **complete, deeply detailed, GitHub-quality** explanation of OFDM, suitable for:
- Telecommunications engineering (EE, EIE, CS)
- LTE / WiFi / 5G / SDR development
- Research notes
- Exam preparation
- GitHub project documentation

English = technical explanation  
Chinese = intuitive explanation for full understanding  

---

# 📚 Table of Contents

- [1. Introduction](#1-introduction)
- [2. Why OFDM? (Single-Carrier Limitations)](#2-why-ofdm-single-carrier-limitations)
- [3. OFDM Concept](#3-ofdm-concept)
- [4. Orthogonality](#4-orthogonality)
- [5. Time Domain vs Frequency Domain](#5-time-domain-vs-frequency-domain)
- [6. FFT/IFFT Engine](#6-fftifft-engine)

---

# 1. Introduction

OFDM = **Orthogonal Frequency Division Multiplexing**  

A multi-carrier modulation technique where:

- One high-rate stream → split into many low-rate sub-streams  
- Each sent on a different **orthogonal subcarrier**  
- Combined using **IFFT**, separated using **FFT**

📌 中文解說：  
OFDM 把高速資料拆成多條慢速小路（subcarriers），每條互不干擾（正交），非常抗多路徑、抗頻率選擇性衰落。

---

# 2. Why OFDM? (Single-Carrier Limitations)

High data rate ⇒ short symbol duration ⇒ high ISI:

```
time →
|symbol1|symbol2|
        ↑ overlap (ISI)
```

ISI (Inter-Symbol Interference):

- Caused by multipath delay spread τ  
- Requirement for ISI-free:

Ts >> τdelay

📌 中文：  
單載波速度太高 → Ts 超短 → 多路徑的延遲比 Ts 還長 → 符號互相重疊 → ISI 災難。

---

# 3. OFDM Concept

OFDM solves ISI by:
- Making each subcarrier **low rate**
- So Ts becomes long
- Avoiding ISI even under large τ

```
Serial bits → S/P → many slow subcarriers
```

📌 中文：  
高速變多條慢速 → 慢速不怕延遲。

---

# 4. Orthogonality

Two subcarriers fk and fm are orthogonal if:

∫ sin(2πfk t) sin(2πfm t) dt = 0 (k ≠ m)

Frequency-domain explanation:

- Each subcarrier has a sinc-shaped spectrum  
- Zero-crossings align exactly with neighboring peaks  
- They can overlap in spectrum **without interference**

ASCII:

sinc1:  |~~~~peak~~~~|
             |0|
sinc2:         |~~~~peak~~~~|

📌 中文：  
零點剛好踩在隔壁 subcarrier 的峰值外 → 完全不互相干擾。

---

# 5. Time Domain vs Frequency Domain

- Frequency-domain symbols mapped to subcarriers  
- IFFT → generates time waveform  
- FFT → recovers individual subcarriers  

📌 中文：  
IFFT 合成；FFT 拆解。

---

# 6. FFT/IFFT Engine

Transmitter:

bits → mapping → IFFT → add CP → DAC → TX

Receiver:

RX → ADC → remove CP → FFT → demap → bits

📌 中文：  
IFFT 把所有 subcarrier 加起來，FFT 幫你拆開。

---



# 7. Subcarriers, Symbols, and Modulation

Modulation per subcarrier:

| Modulation | Bits per symbol |
|------------|-----------------|
| QPSK       | 2               |
| 16-QAM     | 4               |
| 64-QAM     | 6               |

Resource Element (RE):
```
RE = 1 subcarrier × 1 OFDM symbol
```

📌 中文：  
RE 是 LTE 的最小「時間 × 頻率」小格子。

---

# 8. OFDM Signal Construction

OFDM symbol definition:

x[n] = Σ (k=0 to N−1) X_k · e^(j2πkn/N)

This is literally the **IFFT**.

📌 中文：  
subcarrier 的星座點 Xk 經 IFFT → 組合成一條 OFDM 波形。

---

# 9. Cyclic Prefix (CP)

Purpose:
- Remove ISI  
- Convert linear convolution to circular convolution  
- Preserve orthogonality

ASCII:

|---original symbol---|
|CP|

📌 中文：  
CP 是把符號尾巴複製到前面，抵銷多路徑延遲。

---

# 10. LTE OFDM Parameters

LTE DL uses:

- Subcarrier spacing: **15 kHz**  
- Symbol duration: **66.67 µs**  
- RB bandwidth: **180 kHz**  
- System bandwidth = (#subcarriers × 15 kHz)

📌 中文：  
15 kHz spacing 永遠固定；頻寬變大 = subcarrier 變多。

---

# 11. Resource Grid (RB / RE / Symbols)

```
1 RB = 12 subcarriers × 7 symbols = 84 RE
```

ASCII:

12 SC →
-------------------------
|x|x|x|x|x|x|
|x|x|x|x|x|x|   7 symbols
|x|x|x|x|x|x|
-------------------------

📌 中文：  
Scheduler 只分 RB，不分 RE。

---


# 12. Multipath, Fading & Equalization

Multipath causes **frequency‑selective fading**.  
OFDM converts a wideband channel into **many narrowband flat channels**:

- Each subcarrier experiences flat fading.
- Equalization becomes 1‑tap per subcarrier.

📌 中文：  
OFDM 把「寬頻道」拆成「很多窄頻 subcarrier」，每條近似平坦 → 等化變超簡單。

---

# 13. PAPR Problem (Peak-to-Average Power Ratio)

OFDM suffers from **very high PAPR**:

PAPR = max |x(t)|² / E[|x(t)|²]

原因：

- 多個 subcarriers 疊加 → amplitude 可能在某些時間點爆高

影響：

- 功率放大器需要更高線性度  
- PA 效率下降  
- 手機耗電更高

📌 中文：  
OFDM 的波形太「尖」，很吃 PA 性能。

---

# 14. Random Access (RACH) in LTE

RACH is used for:

- Initial cell access  
- Uplink resource request  
- Re-establishing connection  
- Paging response  

**RA slot bandwidth = 1.08 MHz = 6 RB**

UE sends a **preamble** (Zadoff–Chu sequence).

中文：  
RACH 就是 UE「按門鈴」，告訴基地台它想連線。

---

# 15. Fast Retrial Algorithm

Based on **Slotted Aloha**:

- If collision occurs → UE retries **next slot**
- UE will retry on a **different frequency channel**
- Max retries = **M**
- Exceed M → **random backoff**

ASCII:

time →
freq ↓
[x] collision  
 ↓ retry  
[ ] success  

📌 中文：  
撞車 → 下一個時槽換另一條頻率重試 → 效率比純 Aloha 好。

---



# 16. Mathematical Analysis

## 16.1 Arrival Rate Equation

Total arrivals per slot:

λ_T = λ + pγλ_T

Where:
- λ = new UE arrivals  
- p = collision probability  
- γ = fraction of collided UEs that perform fast retrial  

中文：  
λ_T 包含「新來的 UE」＋「上一 slot 撞車後重試的 UE」。

---

## 16.2 Collision Probability

From Poisson arrivals:

p = 1 − e^(−λ_T / N)

Where:
- N = number of RA channels (subcarriers)  

中文：  
成功機率 = e^(−λ_T/N)  
撞車率 = 1 − 成功機率。

---

## 16.3 Fast Retrial Ratio

γ = (1 − p^M) / (1 − p^(M+1))

Meaning:
- Larger M → more aggressive retrials → higher γ  
- γ increases λ_T → system risk overload  

中文：  
M 越大，UE 重試越多次 → traffic 增加 → 撞車變嚴重。

---

## 16.4 Throughput

T = λ_T e^(−λ_T/N)

Interpretation:
- When λ_T/N ≈ 1 → throughput maximized  
- Too high λ_T → collision dominates  
- Too low λ_T → under-utilization  

中文：  
Throughput 的最高值出現在「平均每 channel 1 個 UE」時。

---

# 17. Full OFDM System Diagram

```
                 +----------------+
bits → mapping → |      IFFT      | → CP → DAC → TX
                 +----------------+

RX → ADC → remove CP → +---------------+ → demap → bits
                        |      FFT      |
                        +---------------+
```

中文：  
這是完整 OFDM baseband 的 TX/RX 流程。

---

# 18. References

- 3GPP TS 36.211 – Physical Channels  
- Rappaport – Wireless Communications  
- Proakis – Digital Communications  
- Goldsmith – Wireless Communications  
- EE4316 Lecture Notes  
- 5G NR Standard (38-Series)  

---

# ✔ End of README  
OFDM 教科書級 GitHub README 已完整生成。
