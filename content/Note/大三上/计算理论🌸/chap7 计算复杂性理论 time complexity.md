---
publish: true
created: 2026-10-06T03:04:40.699Z
modified: 2026-11-05T14:41:51.874Z
---

> 如何将数学推理形式化？

computability theory-complexity theory：Is A decidable with restricted resources?

讨论的都是可决定的语言，且关注最差情况复杂性而不是平均复杂性，不依赖模型选择的问题

$a^kb^k$语言优化到nlogn：隔一个划掉一个，然后比较两个的奇偶校验性（等价于k的二进制表示，如果每次的奇偶校验结果都一样那么最开始的k应该是一样的），无法使用小于nlogn的时间复杂度但是不讲证明
如果有俩带子那么可以直接到O(n)
【模型依赖性】计算复杂性理论取决于使用哪种模型，所以一般关心一些模型影响不大的问题，即关注指数和多项式之间的区别
任何小于nlogn的语言都是常规的
【多带tm转换到一带tm最多平方一下时间】合成到的带子长会是O(t(n))，执行一步需要t(n)的时间，之后至少需要执行t(n)步，所有有一个平方
所有可执行的确定机器都是多项式相关的
【class P】多项式时间可决定问题，在不同的模型下都是一样的类，所以用什么方便的模型都可以

# P\&NP, SAT, 多项式时间可规约性

简单地翻转一下nondeterministic-machine的答案是不对的，构不成另一个non-deterministic的TM
【satisfiability problem】是否有一种赋值方法使得一个布尔表达式为true
所有的t-recog问题都可以归约到ATM问题之中
【cook-Levin理论】如果SAT是p的，那么p=np
哦我懂了 就是把一个问题转化为另一个问题
～离散数学\&ads
所有的$NP\le_p SAT \le_P 3SAT \le_p CLIQUE$
【NP-complete】
![[高级数据结构基础（ADS）#NP问题]]
![[图源/图源2/Pasted image 20261008121241.png]]
SAT归约到HAMPATH
【Cook-Levin Theorem】统一地用TM和计算历史表示了然后归约到SAT
![[图源/图源2/Pasted image 20261009112044.png]]
![[图源/图源2/Pasted image 20261009112108.png]]
![[图源/图源2/Pasted image 20261009112337.png]]
![[图源/图源2/Pasted image 20261009112421.png]]
![[图源/图源2/Pasted image 20261009112459.png]]

【3SAT is NP-complete】
![[图源/图源2/Pasted image 20261009110853.png]]
我懂了 这个到3SAT的转换就是如果满足与或的条件就一定满足右边增加过变量之后的3SAT（4个一组）如果不满足与或条件那么4个中有一个必会挂掉，最后判定这个式子成立其实只归约到了最后4k+1的单独的+1那个；这里把与或符号都是用zi代替掉了
