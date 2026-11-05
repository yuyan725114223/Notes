---
publish: true
created: 2026-10-09T03:43:59.054Z
modified: 2026-11-05T14:41:58.041Z
---

对于一个$t(n)\ge n$，有

- $TIME(t(n))\subseteq SPACE(t(n))$
- $SPACE(t(n))\subseteq TIME(2^{O(t(n))})=\cup _cTIME(c^{t(n)})$
  因为不能使用相同的配置，因为默认是decider不能重复已经做过的步骤
- 那么就有$P\subseteq PSPACE$
  【$NP\subseteq PSPACE$】

1. 先证明SAT是PSPACE的（这里说明的方法就是 给每个变量分配了一个空间，然后使用这种分配方式来说明）
2. 再简单说明了一下如果A多项式归约到B并且B是PSPACE的那么A就是PSPACE的
   【$coNP\subseteq PSPACE$】因为PSPACE=coPSPACE，但是尚且不知道P是否=PSPACE，如果P=NP的话这个就是真的
   ![[图源/图源2/Pasted image 20261011204603.png|300]]

## 🌰

【TQBF】【quantified Boolean formula】有条件的boolean formula，比如任意和存在命题
应该是PSPACE的，但我感觉这个应该和SAT那个判定差不多啊 就是给每个变量分配一个位置然后开始undeterministic地递归，递归深度n每次space1所以是O(n)的
哦古老的离散数学
【$LADDER_{DFA}\in NPSPACE$】non-deterministic的情况是自然的
证明也是PSPACE的：加一个限制，要求在b步之内从初始字符串每次改变一个字母到最终的字符串
感觉进行空间优化的算法都完全不需要考虑时间
进一步到pspace的算法就是设置一个中间变量，然后对前半段、后半段盒中间的1/2的步长分别归约

# PSPACE completeness

事实上PSPACE=NPSPACE？？！！🤯
【Savitch‘s Theorem】任意一个$f(n)\ge n, NSPACE(f(n))\subseteq SPACE(f^2(n))$
和LADDER完全一样的方法来构造：看看从开头的计算历史到最后的计算历史是否有一个中间的计算历史使得TM在开始一半的步数内从开头走到中间再在剩下的时间中从中间走到结束（完全一样吧！）
【PSPACE-complete】有一个多项式空间的问题并且多项式空间内的所有问题都可以多项式时间归约到这个问题
多项式空间而不是时间归约，因为reduction应该比class弱一点，不然一个类之内所有的问题都是complete的
【TQBF is PSPACE-complete】有点类似于证明SAT是NP-complete的
第二次尝试失败了因为使用了太多的公式，所以递归最终的时间成本依然达到了指数级别（check-in说明没有使用“任意”是错误的）
然后使用了二合一大法成功降级到n^k

# 将TQBF归约到Geometry game

![[图源/图源2/Pasted image 20261018110632.png]]

# log space

【定义】只有足够的空间来写下输入的指针/一些固定数量的指针；此时输入不计算在内否则输入就会超过n

- 采用新模型：一条只读纸带&一个读写工作tape
  【L】SPACE(log n)
  【NL】NSPACE(log n)
  回文串在L中，因为是
  【L是多项式时间内可解决的】因为log space对应到指数级的时间开销
  【NL-complete】所有NL的问题都可以L归约到该问题的问题
  【Log-space reducibility】一个三带图灵机：一个只读n输入条带、一个读写工作区log n条带、一个只写输出条带
  【PATH is NL-complete】
  【NL=coNL】
  【仅需证明path非是NL的】
  因为不同的co-NL问题可以按照相同模式归约到path非，path非又是NL的，那么所有coNL问题都是NL的，那么NL=coNL
  哦这里必须要知道总共有多少个是可达的，因为需要知道然后区分是猜错了还是不可达；这里的个数是很好算的😋

# Hierarchy theorm

$L\subseteq NL\subseteq P\subseteq NP\subseteq PSPACE$
说明在地址扩大之后tm确实能做的东西更多了：NP！=PSPACE
对于任意一个需要O(f(n))的
【oracle】
