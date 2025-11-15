# GPRS COMPLETE SUMMARY (可編輯版)

## 1. 基本概念 Overview

GPRS（General Packet Radio Service）是 GSM 的 **封包交換擴充技術**，屬於 2.5G。 它讓 GSM 從只能做語音（Circuit Switching）擴展到支援資料（Packet Switching）。

GPRS 主要特點：

- 多使用者共享無線資源（PDCH）
- 非連續式傳輸（bursty traffic）非常有效率
- 不需要像語音一樣整條電路保留
- 支援 always-on 服務（idle 狀態不需釋放 session）

GPRS 架構主線： MS（Mobile Station） → BTS → BSC/PCU → SGSN → GGSN → 外部 PDN（Internet）

GPRS 的主要優勢：

- 更高的資料速率（14–115 kbps）
- 更高的頻譜效率（shared channels）
- 支援 IP-based services（Email、Web、RTP、位置回報）
- 和 GSM 兼容（不需大改整個網路） GPRS = GSM 的封包交換延伸 (2.5G) 多人共享 PDCH（packet data channel） 適合 bursty small packets（Email、Web）

架構主線： MS → BTS → BSC/PCU → SGSN → GGSN → Internet

## 2. PCU (Packet Control Unit)

PCU（Packet Control Unit）是 GPRS 在 BSS 裡面新增的「資料控制大腦」。 若沒有 PCU，GSM BSS **無法處理封包式業務**。

PCU 四大核心功能（更詳細版）：

### 2.1 Radio Resource Allocation（無線資源分配）

- 決定哪些時槽是 TCH（語音）
- 哪些時槽是 PDCH（資料）
- PDCH 可動態調整（根據流量）
- 支援多用戶共享 uplink/downlink

### 2.2 RLC Segmentation / Reassembly（封包切割與重組）

- 把大資料封包切成 RLC blocks（20ms 單位）
- 接收端將 block 重組成原封包
- 做 RLC ARQ（自動重傳）以確保可靠度

### 2.3 MAC Layer Scheduling（排程與存取控制）

- 決定哪一個 MS 先傳資料（公平性、優先級）
- 控制 uplink access（使用 USF 訊息）
- 控制 downlink 分發（Block Allocation）

### 2.4 BSSGP + Gb Interface（與 SGSN 交換封包）

- PCU 透過 Gb 介面使用 BSSGP 協定向 SGSN 上送資料
- 進行 Flow Control（避免塞爆 SGSN）
- 添加 QoS、TLLI、Cell ID 等 metadata

PCU 部署位置：

- 放在 BSC（標準做法）
- 或放在 BTS（更貼近無線，但成本高）
- 或 external PCU-SN（舊網升級用） PCU = GPRS 的 BSS 大腦

PCU 四大功能：

- 分：分配 PDCH（packet data channel）
- 切：RLC segmentation（切封包）
- 排：MAC scheduling（排程多用戶）
- 送：BSSGP + Gb（送封包到 SGSN）

PCU 位置：

- BSC（最常見）
- BTS
- 外置 PCU-SN

## 3. SGSN (Serving GPRS Support Node)

SGSN 是 GPRS 的「核心控制節點」，類似於 GSM 的 MSC，但針對封包。

### SGSN 的完整功能清單（進階版）：

### 3.1 Mobility Management（行動性管理）

- 執行 GPRS Attach / Detach
- Routing Area Update（RAU）
- Cell Update
- 追蹤使用者的位置（RA-level granularity）

### 3.2 Session Management（會話管理）

- PDP Context Management
- 建立 / 修改 / 釋放 PDP Context
- 控制 QoS Profile（bitrate、priority）

### 3.3 Security Functions（安全功能）

- Authentication（透過 HLR/AuC）
- Ciphering（無線加密）
- Integrity 保護
- 分配 TLLI（Temporary Logical Link Identity）保護隱私

### 3.4 Packet Routing / Forwarding（封包路由）

- RLC/MAC 上送的資料由 SGSN 送往 GGSN
- Downlink 封包由 SGSN 送往正確 cell / PCU
- SGSN 是 user-plane 的中繼站

### 3.5 Charging & QoS（計費與服務品質）

- 收集 GPRS CDR（Charging Data Record）
- 記錄：bytes、session time、APN、IMSI、IMEI
- 提供 QoS enforcement（可靠度、延遲） (Serving GPRS Support Node) SGSN = GPRS 的核心「區域大腦」

主要功能：

- GPRS Attach
- Routing Area Update (RAU)
- PDP Context Management
- Security（Authentication / Ciphering）
- Routing / Forwarding 到 GGSN
- Charging Record（SGSN CDR）

關鍵：SGSN = 位置管理 + Session 管理 + 安全 + 封包路由 + 計費

## 4. GGSN (Gateway GPRS Support Node)

GGSN 是通往外部資料網路（PDN）的閘道，相當於一個大型 ISP Router + Firewall。

### GGSN 的完整功能：

### 4.1 IP Address Management（IP 位址管理）

- 為使用者分配 IP（動態/靜態）
- 或透過 DHCP relay 分配 IP

### 4.2 APN Resolution（APN 對應 GGSN）

- 不同 APN（internet、intranet、m2m）對應不同 GGSN
- DNS 查詢 APN → GGSN IP

### 4.3 GTP Tunnel Management（隧道管理）

- 建 GTP-U（user plane）隧道
- 管理 GTP-C（control plane）信令

### 4.4 Traffic Filtering / Firewall

- 過濾非法流量
- APN-based firewall

### 4.5 Charging Functions（計費）

- 產生 GGSN CDR
- 記錄外網流量 bytes
- 供計費系統使用 (Gateway GPRS Support Node) GGSN = 封包出口大門

功能：

- IP Address Allocation（或 DHCP）
- APN Routing（不同 APN → 不同 GGSN）
- Firewall/Filtering
- GTP Tunnel（SGSN ↔ GGSN）
- Charging Record（GGSN CDR）

核心：GGSN = 分配 IP + 管 GTP 隧道 + 通往 Internet 的門

## 5. Protocols / Interfaces

- Gb：PCU ↔ SGSN（BSSGP）
- Gn：SGSN ↔ GGSN（GTP）
- Gi：GGSN ↔ Internet

## 6. GPRS Logical Channels

- PDTCH：傳資料
- PRACH：Random Access（手機舉手）
- PAGCH：Access Grant（告訴你 PDCH）
- PACCH：控制（ACK、power control）

## 7. GPRS Procedures

### Attach

手機登入 SGSN → 認證 → 附掛成功

### PDP Context Activation

SGSN 向 GGSN 取 IP → 建立 GTP Tunnel → 通知 MS → MS 開始上網

### Routing Area Update (RAU)

MS 移動到新的 RA → SGSN 更新位置

## 8. DNS / DHCP / Charging

### DNS

- 查 APN → GGSN IP
- 一般網域查詢

### DHCP

- 分配 IP / DNS 給用戶（由 GGSN 代處理）

### Charging Gateway

- 收 SGSN/GGSN CDR
- 計費（per KB/MB）
- 傳到 Billing System

## 9. Coding Schemes

- CS-1：最慢、最強保護
- CS-2：中
- CS-3：中高
- CS-4：最快、最弱保護

訊號差 → CS-1 訊號好 → CS-4

## 10. Impact Summary

### Impact on BTS

- 支援 CS-1
- 處理 PDCH、GPRS burst
- 與 PCU 協同 RLC/MAC

### Impact on BSS

- 加入 PCU
- 支援 BSSGP

### Impact on NSS

- 增加 SGSN
- 增加 GGSN
- DNS / DHCP
- Charging Gateway

## 11. 深化版：PCU / SGSN / GGSN 詳細技術講解

### 11.1 PCU 深化版（技術級細節）

- PCU 其實是 GPRS 的「BSS 層分層點」，處理 RLC/MAC
- 在 Downlink：PCU 根據 TBF 狀態將 LLC PDU 切割為 RLC blocks
- 在 Uplink：PCU 根據 USF（Uplink State Flag）通知 MS 何時能發送
- PCU 做 Flow Control，避免 SGSN buffer overflow（BSSGP）
- PCU 與 BTS 之間需要實作 PDCH mapping（TS → PDCH）
- 支援 Dynamic PDCH allocation（語音忙時可歸還時槽）

### PCU ASCII 示意圖：

```
        +-----------+     Gb      +-----------+
MS ---> |  BTS/BSC  | ----BSSGP-->|   SGSN    |
        |   + PCU   |             +-----------+
        +-----------+
            |  ^
         RLC|  |MAC
            v  |
          空中介面
```

---

### 11.2 SGSN 深化版（技術級細節）

- SGSN 為某 routing area 內所有 MS 提供 mobility management
- 處理 GPRS Attach/Detach + Integrity + Ciphering
- 做 PDP Context activation/deactivation（管理 GTP-C）
- SGSN 保留 GPRS Mobility Management (GMM) state machines
- SGSN 負責 APN selection（決定送往哪個 GGSN）
- SGSN buffer 下行資料直到 MS 建立 TBF（下行發送）
- SGSN 維持 subscriber profile（從 HLR 下載）

### SGSN ASCII 示意圖：

```
+--------+     Gb     +--------+     Gn     +--------+     Gi     +--------+
|  MS    | <--------> |  SGSN  | <--------> |  GGSN  | <--------> |  PDN   |
+--------+   RLC/MAC   +--------+   GTP-C/U  +--------+    IP      +--------+
```

---

### 11.3 GGSN 深化版（技術級細節）

- GGSN 是 PDN 的 ingress/egress router
- 為每個 PDP context 分配 IP 地址（動態/靜態）
- 管理 GTP 隧道（建立、維護、移除）
- 做 APN based routing（不同 APN → 不同企業 VPN 或 Internet）
- 做 packet filtering / firewall（例如企業 APN）
- 為 charging 產生詳細流量紀錄（bytes/flows/duration）

### GGSN ASCII 示意圖：

```
              GTP-U Tunnel
   SGSN =================================> GGSN
       
   GTP-C (控制)                 外部網路 (Internet / VPN)
```

---

## 12. GPRS 三大程序流程圖（Attach / RAU / PDP Context）

### 12.1 Attach Procedure ASCII 流程圖

```
MS                     SGSN                HLR
 |  Attach Request  ---> |                   |
 |                        |-- Auth Req ----->|
 |                        |<-- Auth Resp ----|
 |  Ciphering Cmd  <-----|
 |  Ciphering Complete -->|
 |  Attach Accept   <-----|
 |  Attach Complete ----> |
```

重點：Attach = 登入 GPRS 網路 + 身份認證 + 啟動 GMM

---

### 12.2 Routing Area Update (RAU) 流程圖

```
MS                     SGSN(new)         SGSN(old)         HLR
 | RAU Request ---------> |                |                |
 |                        |-- Context Req -->|              |
 |                        |<-- Context Resp--|              |
 |                        |-- Update Loc --> |             |
 |                        |<-- Ack ----------|             |
 | RAU Accept <-----------|                |                |
 | RAU Complete --------->|                |                |
```

重點：RAU = MS 移動到新的 Routing Area 時更新位置

---

### 12.3 PDP Context Activation 流程圖（最重要）

```
MS              SGSN                GGSN               PDN
 | Activate Req --> |                 |                 |
 |                   |-- Create PDP -->|                 |
 |                   |                 |-- IP Allocate -->|
 |                   |                 |<- IP Assigned --|
 |                   |<-- PDP Accept --|                 |
 | <--- Activate Accept               |                 |
```

重點：PDP Context = IP 地址 + QoS + GTP Tunnel。

---

## 最後 5 句金句（考前 1 分鐘）（考前 1 分鐘）

1. GPRS = packet-switched GSM（shared PDCH）。
2. PCU = 分、切、排、送（PDCH、RLC/MAC、BSSGP）。
3. SGSN = attach、RAU、PDP、security、路由、計費。
4. GGSN = IP、APN、GTP Tunnel、出口、計費。
5. DNS 找 GGSN、DHCP 分 IP、Charging Gateway 算錢。

