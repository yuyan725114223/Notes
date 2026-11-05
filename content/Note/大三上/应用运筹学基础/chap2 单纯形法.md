---
publish: true
created: 2026-09-30T05:28:34.538Z
modified: 2026-09-30T06:53:30.272Z
---

通过local search局部搜索来试图搜到一个最优解
凸优化：局部最小就是整体最小
添加一些松弛变量来直接得出一组基解
【典则形式】初始有
$x_B=A_B^{-1}b \ge0$
$x_N=0$
那么

- $x_B=A_B^{-1}b-A_B^{-1}A_Nx_N$
- $z=C_B^TA_B^{-1}b+(C_N^T-C_B^{-1}A_B^{-1}A_N)x_N$
