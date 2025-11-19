📘 Location Update Analysis 筆記（完整中文版 + GitHub Block Math 格式）
1. Location Update 的目的

手機在移動時，系統不需要知道它的「cell」，只需要知道它所在的「Location Area (LA)」。只要跨區（換 LA），就要做 Location Update (LU)。

觸發 LU 的情況：

跨越 LA → 最常見

定期更新（Periodic LU）

開機登入（IMSI Attach）

2. Location Area (LA) 的定義

一個 LA 包含多個 cell

每個 LA 有一組 LAI（Location Area Identity）

MS 發現 LAI 變了 → 觸發 LU

LA 大小的設計取捨：
太小 → Signaling 高	太大 → Paging 高成本

✅ 設計 LA 時，要平衡 update cost 與 paging cost。

3. LU 的三種類型（成本遞增）
類型	描述
Intra-VLR LU	在同一個 VLR 裡移動
Inter-VLR (TMSI)	換 VLR，但有 TMSI
Inter-VLR (IMSI)	換 VLR 且沒有 TMSI → 最貴
4. Location Update 到達率（λ）
𝜆
=
𝑣
𝜌
𝐿
𝜋
λ=
π
vρL
	​

符號	意義

𝑣
v	MS 的移動速度

𝜌
ρ	用戶密度（MS/km²）

𝐿
L	LA 外圍邊界長度

𝜋
π	平均常數
5. LA 的外圍邊界長度 L
𝐿
=
6
𝑅
(
1
3
+
1
2
3
𝑁
−
3
)
L=6R(
3
1
	​

+
2
1
	​

3N−3
	​

)
符號	意義

𝑅
R	Cell 半徑

𝑁
N	LA 裡的 Cell 數
6. 外圈產生 LU 的 Cell 數（
𝑁
𝑝
N
p
	​

）
𝑁
𝑝
=
6
𝑁
3
−
3
N
p
	​

=6
3
N
	​

	​

−3

只有外圈 cell 才會觸發 LU。

7. 無線資源耗用（以 cell j 為例）
𝑇
𝑟
𝐿
𝑈
(
𝑗
)
=
𝜆
𝐿
𝑈
(
𝑗
)
[
∑
𝑖
=
1
3
𝑃
𝐿
𝑈
(
𝑖
,
𝑗
)
𝑡
𝐿
𝑈
(
𝑖
)
]
Tr
LU
(j)
	​

=λ
LU
(j)
	​

[
i=1
∑
3
	​

P
LU
(i,j)
	​

t
LU
(i)
	​

]
符號	意義

𝜆
λ	LU 到達率

𝑃
(
𝑖
,
𝑗
)
P
(i,j)
	LU 類型佔比

𝑡
(
𝑖
)
t
(i)
	該類型 LU 所耗時間
8. MSC/VLR 的 DB 操作量
𝑇
𝑇
𝑁
𝐿
𝑈
=
∑
𝑗
=
1
𝑁
𝐿
𝐴
𝑁
𝑝
𝜆
𝐿
𝑈
(
𝑗
)
[
∑
𝑖
=
1
3
𝑃
𝐿
𝑈
(
𝑖
,
𝑗
)
𝑇
𝑁
𝐿
𝑈
(
𝑖
)
]
TTN
LU
	​

=
j=1
∑
N
LA
	​

N
p
	​

	​

λ
LU
(j)
	​

[
i=1
∑
3
	​

P
LU
(i,j)
	​

TN
LU
(i)
	​

]
LU 類型	DB 操作次數
Intra-VLR	2 次
TMSI LU	14 次
IMSI LU	16 次
9. 範例參數（PDF 頁 10–18）

𝜌
=
10000
ρ=10000

𝑅
=
0.5
R=0.5

𝑣
=
10
v=10

𝑁
=
10
N=10

每 MSC 包含 5 個 LA

10. Case 1：全部 Intra-VLR

邊界長度：

𝐿
=
1.377
L=1.377

LU 到達率：

𝜆
=
43831
λ=43831

無線耗用：

𝑇
𝑟
=
43831
×
0.6
3600
=
7.3
 
E
r
l
a
n
g
s
Tr=
3600
43831×0.6
	​

=7.3 Erlangs

所需 channel：約 14

11. Case 2：全部 Inter-VLR（80% TMSI, 20% IMSI）

平均時間：

𝐸
[
𝑡
]
=
0.8
×
3.5
+
0.2
×
4
=
3.6
E[t]=0.8×3.5+0.2×4=3.6

無線耗用：

𝑇
𝑟
=
43.83
 
E
r
l
a
n
g
s
Tr=43.83 Erlangs

所需 channel：約 57

12. MSC/VLR 的 DB Transaction（跨 5 LA）

外圈 cell 數：

𝑁
𝑝
=
6
10
/
3
−
3
≈
8
N
p
	​

=6
10/3
	​

−3≈8
類型	Cell 數
Intra-VLR	
3
𝑁
𝑝
=
24
3N
p
	​

=24
Inter-VLR	
2
𝑁
𝑝
=
16
2N
p
	​

=16

總 DB 操作：

𝑇
𝑇
𝑁
=
43831
(
48
+
230.4
)
=
1.22
×
10
7
TTN=43831(48+230.4)=1.22×10
7
13. 總結重點

LU 幾乎都來自外圈 Cell

IMSI LU 成本最高（時間 + 計算量）

LA 太小 → signaling 成本過高

LA 太大 → paging 範圍過廣

🎯 最佳 LA 大小 = 更新成本 + 分頁成本最小化

14. 考試金句（背熟！）

LU rate：

𝜆
=
𝑣
𝜌
𝐿
𝜋
λ=
π
vρL
	​


外圈 Cell 才會觸發 LU

IMSI LU 最耗資源

Case 2 ≈ Case 1 的 6 倍成本

高峰每小時 DB 操作量：

𝑇
𝑇
𝑁
≈
1.22
×
10
7
TTN≈1.22×10
7
