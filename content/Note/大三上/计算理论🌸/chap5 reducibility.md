---
publish: true
created: 2026-09-12T02:08:47.185Z
modified: 2026-11-03T07:43:23.778Z
---

通过一个已经知道是undecidable/unrecognizable的问题来证明另一个问题是undecidable/unrecognizable的：通过把一个问题转化为更简单的问题来使问题更容易的方法
\*证明停机问题判定的图灵机语言是undecidable的，通过构造一个ATM
很好构造啊 如果不停一定是拒绝的 如果是有限的那么就运行判断一下是停还是不停，如果停机是decidable那么ATM就是decidable的，和已知结论矛盾了，那么停机问题应该就是undecidable的
A可归约为B意思是 可以用B来解决A
如果A是困难的，那么B也是困难的，因为如果B是简单的，那么A理应也是简单的
【computable】认为一个function f是可计算的如果一个TM有输入w并且停下来的时候输出是f(w)
【mapping-reducible】如果有一个函数f，w是A并且f(w)是B的，那么认为A可以映射归约到B

- 如果B是decidable/T-recognizable的那么A也是decidable/T-recognizable的
- 如果A是不可判定的那么B也是undecidable的，应用反证法
  太强了简直是天才…
  还要看看有哪些是只有mapping reducibility有的但是reducibility没有的定理 #todo

# 【computation history method/计算历史方法】

【Hilbert的第十个问题】找到一个算法来判定一个多项式是否具有一个整数解
答案是没有
通过证明$A_{TM}$可以推导到D（所需要找的算法）上，然后通过A undecidable来推出D是undecidable
有点多 决定转向post corresponding problem讲一讲
【post corresponding problem】寻找一个有限长度的domino的排列使得上面的字符串连接-下面的字符串连接；这里的domino是可以重复使用的

- 想要证明判断一个P是否有match的问题是undecidable的
- PCP=有match的P的语言，然后证明ATM可以推导出PCP
  【TM configurations】一个三元组（q, p, t）这里q是state，p是head position，t是tape contents
  把这个三元组表示为$t_1qt_2, t=t_1t_2$并且头位置在$t_2$的开头
  【计算历史方法】就是把一个接受的配置序列写成一串，中间用#分割
  【LBA/linearly bounded automoton】单-tape的图灵机，它的带子是有限长的，TM的读写头无法离开tape到有限长之外的部分
  A\_LBA是decidable的，因为如果是一个有限长的磁带，那么状态数量
- PCP是undecidable的：
  通过A\_TM可以归约为PCP来说明
  即如果有一个TM可以decide PCP，那么就可以通过这个构造出一个TM来决定A\_TM，这绝对是错的，因为ATM undecidable
  ![[图源/图源2/Pasted image 20261003150953.png]]
  具体的构造方法
- ALL\_CFG（判断一个cfg的语言是否是所有字符的闭包）是不可判定的
  ![[图源/图源2/Pasted image 20261003160149.png]]
