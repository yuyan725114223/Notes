---
publish: true
created: 2025-09-15T05:56:55.287Z
modified: 2026-09-14T11:08:52.437Z
---

## 注意点

变量名一致。比如别把Element写成element

## 不可能三角

效率、全面、最优解

## 学点英语

asymptotically equivalent：渐进等价

# 树

### 一些规定

_规定空树的高度为-1_

## AVL树

### 目标：节省开销 _和二叉查找树相比_

```
评估：节点的高度之和平均
```

### 平衡性

#### 衡量：balance factor：定义为左右子树高度差(L-R)，应该在（-1，1）

直接用BF就行，不用单独记高度差（求和即可）

#### 维护：左/右旋选择更改

1. 单旋
2. RRrotation：trouble maker在trouble finder右边的右边（指的是子树的位置），以此类推
   特性：==插入（有旋转）前后树高不变==
   更新高度优先调整一开始出现问题的子树
   剩下插入到哪里：注意始终都是==查找树==。
   Insert 28, 23, 54, 61, 98, 37 into an initially empty AVL tree first. Then immediately insert one of the following keys. Which one will cause an RL rotation?
   A.10
   B.30
   C.60
   D.70

> D. 确实位置要对，但是也要旋啊！

## splay tree：不增加额外的开销完成AVL的使命

平均开销logN：均摊开销
_大致思想：每个被access的node都被旋转到根上_

### 具体操作（维护）

zig-zag：左右旋，同AVL情况
zig-zig：Grandparents两层同左右，但是先旋祖父再旋父亲
zig：单旋：父亲是根结点

### 平均开销算法：Amortized Analysis

> Amortized bounds are weaker than the corresponding worst-case bounds, because there is no guarantee for any single operation.
> 确实认为它weaker💦

#### 加和平均：Aggregate analysis

直接算 **n 次操作的总开销**，再除以 n

#### Accounting method：记账法（本次使用）

给每个操作分配一个“费用”，让便宜操作多付一点，存起来用来支付之后的贵操作。

1. 动态数组插入时，每次多交一点“钱”，当扩容时就有足够的余额支付复制开销。
2. e.g.  运用势能差来估计$\hat c$和c的差，只需每个势能都大于零（且最开始为0）（事实上只需assume minimum at the start of the sequence），就可以用好算的$\frac{\Sigma \hat c}{n}$来代替$\frac{\Sigma c}{n}$估计总开销

#### Potential method（本次也使用了）

- 类似物理里的“势能”，定义一个 **势能函数** 表示数据结构的状态。
- 每个操作的均摊成本 = 实际成本 + 势能变化。
- 保证整体不会亏。

### 证明为何Splay tree可行

![[图源/图源1-7.30/Pasted image 20250915165225.png]]
$\phi(T)$势能的定义如上

In typical applications of data structures, it is not a single operation that is performed, but rather a sequence of operations, and the relevant complexity measure is not the time taken by one operation but the total time of a sequence. Hence instead of imposing any explicit structural constraint, we allow the data structure to be in an arbitrary state, and we design the access and update algorithms to adjust the structure in a simple, uniform way, so that the efficiency of future operations is improved. We call such a data structure **self-adjusting**. For example skew heaps and splay trees are such kind of structures.
Which one of the following statements is FALSE about self-adjusting data structures?
A.They need less space, since no balance information is kept.
B.Their access and update algorithms are easy to understand and to implement.
C.In an amortized sense, ignoring constant factors, they can be at least as efficient as balanced structures.
D.Less local adjustments take place than in the corresponding balanced structures, especially during accesses.

> A.只能说确实少了吧😂

## 红黑树：节省删除时的操作数目

叶子节点公用，只有internal node存值
子结构识别
==注意是node还是internal node==
一般默认2-3/4树是B+树（父亲节点是leaf的key）

> A 2-3 tree with 3 nonleaf nodes must have 18 keys at most.
> 对的，因为只有最后一层有key，而且上层的所有都带两个作为分界，最后一层有三个值。所以总的key个数=3\*6=18个

# 搜索

stemming：保留最初始的词：如a, the, it, etc.
停用词：对常见的无意义词不分析

> When evaluating the performance of data retrieval, it is important to measure the relevancy of the answer set.
> 错的。不是relevancy of the answer set而是relevancy of用户查询and取回的数据。不是
> “系统的属性”，而是“结果与查询之间的关系

> Which of the following is NOT concerned for measuring a search engine?
> A.How fast does it index
> B.How fast does it search
> C.How friendly is the interface
> D.How relevant is the answer set
> C.属于人机交互而不是搜索引擎性质

分布式：根据文档分布式，提高抗灾能力
实现：即分别记录各个词对应的文档及在文档中出现的位置
data retrieval：关注响应时间和index分布；information retrieval：关注正确率
threshold只按动态参数过滤文档，不挑选出最高的top-k

> For the document-partitioned strategy in distributed indexing, each node contains a subset of all documents that have a specific range of index.
> 对啊这就是根据文档进行分布式

# binomial

二项树：由两个小的二项树组合而成
使用：若干个二项树组合成二项队列
维护：类似二进制加减法，只需把大的根接入小的根即可
查询：有时会维护一个min指针，将O(logN)降为O(1)
队首出队：将一些小的子二项树与剩下的二项树合并，类似上述

# backtracking

```
def property(perm: list) -> bool:
    for k in range(0, len(perm)):
        for j in range(0, len(perm)):
            if j < k:
                if perm[k] == perm[j]:
                    return False
                elif abs(perm[k] - perm[j]) == k - j:
                    return False
    return True

def extend(perm: list, n: int):
    new_perm = []
    for p in perm:
        for i in range(0, n):
            new_perm.append(p + [i])
    return new_perm

def n_queens(n: int) -> int:
    domain = list(range(0, n))
    perm = [[]]
    for i in range(n):
        new_perm = list(filter(property, extend(perm, n)))
        perm = new_perm
    return len(perm)
```

在检测到条件不符合的时候直接卡断->剪枝
写高速公路代码题
斐波那契堆？我对此一无所知

# DP

## 活动

逐个丢进来活动，然后挨个逐个遍历时间和活动个数
非多项式时间，因为T可能很大（二进制数）
控制最优子结构条件？
贪心？
思路：按最早结束时间分化
一定成立，若最优解不是最早时间结束，那么更换成最早结束的事件也成立
之后采用解决这个的子问题即可
_类似于：通过智力人工选择了最优的解，避免了机械比较的时间花费_

# NP问题

## 如何证明是NP问题

比较：需要解决子问题也是NP问题
密码学np困难
统一空间来衡量可解决问题的困难程度：_判定和验证有区别_
NP：可以在polynomial时间内判定但不能在poly时间内解决
exp：可以在指数时间内找到解的问题

使用选择公理解决罗素悖论：设定集合论里面的选择范围

# Approximation

评估问题的近似界（指解的界）
在这里舍弃最优

### 装箱

_在线算法 online algorithm_
Next fit：直接一个一个遍历啊
First fit：并非单调：可能有恰好并入会让箱子个数减小
Best fit：单调
任何一个算法都可以构造出一个例子，使得倍数在大概5/3？
为什么不能先排个序？因为是在线算法（流水线），离线算法就可以排序

### 背包

### K中心（几何）

# local search

可以找到领域中最有利于问题解决的方向
可能陷入局部最优解&不知道效率？
领域在可行域之内；是完整的调整而不是局部最优
凸优化：局部最优解一定是整体最优解
凸化/凸松弛

# 随机化

打破对称性
算法效果随机/结果随机

# parallel algorithm

## 描述方法

可以采用

### PRAM parallel random access machine（强调同时进行）

$for P_i ,  1 \leq i \leq n , pardo$，这里pardo指平行重复运行
$A(i):=B(i)$，这里：=是赋值的意思
EREW/CRCW/etc：E指exclusive，C指concurrent
冲突解决：随机策略/小数优先/common rule：相同则进

### W-D方法，即word-depth（强调分析）

用work W(N)来描述总工作量（操作），depth T(N)描述parallel算法下的总时长（时间）
P(N)=W(N)/T(N)，描述为想要达到T(N)的时间，所需要的平均处理器数目
如果p\<P(N), 则时间为W(N)/p

#### WD-presentation Sufficiency Theorem

$T \leq \frac{W(N)}{p}+T(N)$因为对每一层，有$t(i)=\lceil W(i)/p \rceil \leq W(i)/p+1$则有$\Sigma_{i=1}^{T(N)}t(i)=\frac{\Sigma W(i)}{p}+T(N)=W(N)/p+T(N)$即为最终所得结果。
感觉一下，因为有W(N)和T(N)都有可能卡住进度，所以是和而非MAX

### 比较

- PRAM：专门描述了每个processor的处理方式，可能不那么必要，而且处理器数量并不恒定/这样空闲太多
- W-D：不描述idle

## 简单小🌰

### 找最大值

简单演示了一下PRAM和WD是啥东西

### prefix-Sum

1. A，初始输入，只有最下一层
2. B，两两之和，坐标1层数，2层中序
3. C，从初始节点加到当前节点的最右子节点
   - 注意这里从上到下，关系有
   1. 最左边就是和（1开始）
   2. 右子节点=父节点
   3. 左子节点=父左+当前 _注意这里不用左边的子节点是因为要同时parallel运行_
4. 最后一层的C输出即为所求之和
   自然有时间复杂度T(N)=O(log N), W(N)=O(N)

# Eternal sorting

![[图源/图源1-7.30/Pasted image 20251229161632.png]]
这里merge的意思是哪两个元素正在进行比较

# 习题思考

#### werewolf

最后werewolf？尝试werewolf（N-1人，M-1狼，L谎（判断谎言个数以判定是否合格，<0直接剪枝））；尝试werewolf（N-1人，M狼，若干谎）
剪枝if慌到/狼到/有无狼撒谎（最后）
记录谎言人
判定两个，谎言人个数？狼谎？
数据结构？
图（两个，链接反链接），狼人（数组），谎言人（数组），最后判重复
建图（两个），维护俩表，判重，递归

#### Programming Contest

逐个：个数少1，happy score-当前的score
到1时找剩下的最小的score能够满足happy，不然一直都不happy
从前往后，采用happy递减情形
这样其实还是暴力枚举？
局部最小怎样才能总体最小的问题
想要性价比最高且不溢出太多

先高分到低分排序
int find(i,n,score){
if(i==n&\&score>0)return -1;
if(score<0)return 0;
dp\[s]={0};
int dp1=time\[i]+find(i+1,n,s-score\[i]);
int dp2=find(i+1,n,s);
dp\[s]=min{dp1,dp2};//再判一下两个是不是-1
return dp\[s];
}
这肯定不是dp。没有局部最小。
假设我们有两者同时有序是成立的，低分到高分排。
递推式是什么？
确定最小的然后逐个递推，但还是二维的？；因为同时要确定i和s，且没有函数关系，这两个是不一定一致的
dp\[s]=min{time\[i]+dp\[s-score\[i]],dp\[s]}且这俩都不是-1🤔
关键就是逐个把物品丢进去！

#### to fill or not to fill

如果有可达范围内更便宜的加油站-当然加到更便宜为止，然后递归子问题
如果没有呢？加满
后面还有更便宜的？需要的距离减去到那里需要的最低的油价，如果不够还要多次处理
如果没有了？按油价逐个加到能够跑完全程
跑不完？按最多的算

####  **戳气球（Burst Balloons）**

- **题目描述**：
  有 n 个气球排成一行，每个气球有分数 nums\[i]。
  戳破气球 i 时得分 = nums\[left]\*nums\[i]\*nums\[right]，其中 left/right 是相邻未戳破的气球。
  求戳破所有气球的最大分数。

#### **股票买卖（Best Time to Buy and Sell Stock）**

- **题目描述**：
  给定数组 prices，prices\[i] 是第 i 天股票价格。\
  只能进行一次买卖，求最大收益。
  - 进阶：可以多次买卖，或冷冻期、手续费等限制

#### **子集和 / 分割等式（Partition Equal Subset Sum）**

- **题目描述**：
  给定一个正整数数组 nums，判断是否能将数组分成两个子集，使两个子集的元素和相等。
  NP？不记得了
