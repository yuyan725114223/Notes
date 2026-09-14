---
publish: true
created: 2025-11-15T02:03:51.225Z
modified: 2026-09-14T11:33:31.426Z
---

## 学习计划 ⛽️💪

- VAD：14Chapter！还有八周😖。大概每周要看俩chapter！完全来不及orz。周四到周末每天要看半个chapter。
- ChartGPT：每周两页🤔

## Chap1

### human

目的：聚焦人机交互：dataset/people

#### 用途

- 知道结果-呈现
- 不知：探索parameters
- 自动：人建立信任
- 测试已有模型
- 可视化工具的长期使用

### external visualization

replace cognition with perception
vis:

1. 因为是parallel的，可以有大局同时观测
2. 可以记录/回放且技术发达

all data:

1. details matter:找出工作pattern和不在预期内的观测值

### limit

1. human
2. computation
3. 视觉局限：在杂乱和缺失之前找到美感（sweet spot）
   如何衡量effectively：information density

### Analyze

在决策之前分析作为开始设计的stepping stone

## Chap2 Data abstraction

### Data：关心

#### sematics meaning

世界语义

#### data type

item, attributes, grid, link, position之类：和计算机里面存的不同！
item：individual discrete entity(横向)
attributes: property of item(纵向)
type：
1\. category
2\. ordered: ordinal/ quantitative，这两者的分类取决于是否可计算
方向：单边/双边/环形
grid: 不连续的采样手段
cell：键值对
服务于可视化的绘制

> position不算attribute：因为attribute是附加在顶点上的，而position是顶点的本质信息

#### availability

静态/动态

### dataset type

table, network
field：grids+position+attributes

1. grid type
2. interpolation: 指如何给没有数据的地方补上数据，采用不同的手段
3. sampling
   geometry: field注重空间中的点及其键值，geometry注重空间中的几何结构
   item+position
   cluster(相似聚类)>lists(有序)>sets(无序)
   ![[图源/图源1-7.30/Pasted image 20260228184948.png]]

### 抽象操作

1. 转化：考虑是否需要
2. 数据集大小
   1. 有多少个item
   2. 每个属性大小
      1. 种类多少
      2. range大小
3. 数据集类型，属性类型
   概念/数据模型
   \*温度 抽象可能：连续/种类（是否0度之上）/普通（热/冷/刚好）
   Strahler number：量化一个图中节点的重要程度
   ![[图源/图源1-7.30/Pasted image 20260228184914.png|400]]

## Chap3 抽象任务

action(分析、搜索、etc)+target（操作对象）
分析用户想法：需要发现/展示；enjoy；添加标注/字/导出结论
search：查找，探索，浏览，locate
query（问一个问题）：确定/比较/抽象（1-多）
一般来说要去掉专业名词来让受众理解
![[图源/图源1-7.30/Pasted image 20260228185352.png]]

## Chap4 嵌套模型

### four question

domain users?
abstract

1. What is displayed? data
2. why is needed? task
   how is displayed
3. interaction
4. visualize encoding
   algorithm

> downstream: cascading effects
> 指从上到下逐级传递，domain users决定tasks决定how is displayed决定so on
> upstream: iterative refinement
> 算法迭代优化，改善上游的效果

> 且每级都可能出岔子，逐个攻破：技术-lab study-field study-measure adoption
> problem(design study)/technology driven work
> 性能好！=设计好；lab study不能证明task abstraction是对的
> 需要完整的4个组成部分！不然会有性能缺陷/漏洞

## Chap5：marks\&channels

idiom structure：交互方式
分析：marks：表示的东西：基本几何元素：点线面（interlocking area：指互相重叠的区域）（这样有维度上的限制）；包含（可以嵌套）/连接表示link
channels：视觉通道：表示物体属性的方式
表达力：合适的channel
有效性：选择更好的channel

## Chap7：arrange table

散点图：只有数值没有键值

1. **关于数据墨水比（Data-Ink Ratio）的辩证应用**： 塔夫特（Tufte）主张“最大化数据墨水比”。然而，如果在为一个非专业的大众媒体设计科普图表，你会严格执行这一原则吗？请结合奈杰尔·福尔摩斯（Nigel Holmes）的观点和相关的记忆力研究（如Bateman等人的实验），说明在什么情况下“增加非数据墨水”反而能提高沟通效率。

> 不会。对于媒体设计来说最大化数据墨水比的图表很有可能过于干瘪，难以达到科普的效果，走进人的心里。比如看到一个比较抽象的图表在信息碎片化的时代，用户可能一下就滑走了。bateman等人的实验也说明了这点。在非数据墨水有助于用户理解的时候；区分装饰和垃圾

2. **感知任务阶梯的深层机制**： 克利夫兰（Cleveland）和麦吉尔（McGill）将感知任务按准确度排序，其中“相同轴线上的位置”优于“角度”和“面积”。**为什么**人类大脑在判断长度比判断面积时更准确？这种感知偏差在设计具有诱导性的商业报告时可能会被如何利用？
3. **视觉寻求准则（Visual Information-Seeking Mantra）的逻辑顺序**： 本·施耐德曼（Ben Shneiderman）提出了“先看概览，再缩放过滤，最后按需提供细节”。请分析：如果一个交互式可视化工具跳过了“概览”直接进入“细节”，会给用户的\*\*工作记忆（Working Memory）\*\*带来什么具体挑战？
4. **3D可视化的合理性判定**： 在该领域，“禁止无正当理由的3D（No unjustified 3D）”是一条共识。请举例说明：在处理什么类型的原始数据时，3D表现形式是**必须且合理**的？而对于抽象的统计数据，3D化产生的“透视失真（Perspective Distortion）”会如何破坏图形完整性？
5. **“眼睛胜过记忆（Eyes beat memory）”原则的实践**： 在展示多组数据的趋势对比时，为什么专家（如蒙兹纳）通常建议使用“小倍数图（Small Multiples）”而不是带有时间轴切换的动态动画？请从人类视觉感知的局限性出发进行论证。
6. **极简主义与冗余编码（Redundant Encoding）的冲突**： 塔夫特通常反对冗余（认为应擦除冗余的数据墨水），但为了确保图表对色盲用户或弱视用户的**可访问性（Accessibility）**，设计师往往需要同时使用颜色、形状和标签来编码同一属性。你会如何平衡“极简”与“包容性设计”这两个看似矛盾的目标？
7. **理解“注释层（Annotation Layer）”的编辑角色**： 阿尔贝托·卡罗（Alberto Cairo）批评许多可视化工具只是“把数据扔给读者”。请解释：为什么一个设计精良的交互式工具如果缺乏“注释层”，可能会导致读者的认知失败？设计师在其中扮演的“编辑”角色与“分析师”角色有何不同？
8. **数据密度（Data Density）与认知负荷的权衡**： 塔夫特建议“在合理范围内最大化数据密度”。请描述一个场景：当数据密度过高时，它在什么时候会从“充满证据的美感”转变为“视觉杂讯（Noise）”？你将使用什么视觉手段（如分层、对比度）来缓解这种过载？
9. **颜色感知的相对性与失真**： 为什么在设计灰度热力图时，仅仅依靠数值与灰度值的线性映射是不够的？“亮度对比效应（Simultaneous Brightness Contrast）”会如何导致读者对图表中特定区块的数值产生误读？
10. **DIKW模型在可视化中的转化路径**： 请结合具体的图表元素，解释可视化是如何协助人类完成从原始“数据（Data）”到“知识（Knowledge）”的转化过程的。在这个过程中，可视化的“发现（Discovery）”功能和“呈现（Presentation）”功能分别在什么时候占据主导？
