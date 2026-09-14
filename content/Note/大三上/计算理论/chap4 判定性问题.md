---
publish: true
created: 2026-09-04T08:47:56.371Z
modified: 2026-09-14T11:08:52.463Z
---

# decidability

～怎么看出来机器是在无限拒绝还是只是用了很长的时间？目前还看不出来

- 证明一种语言 含有所有所包括语言为空集的DFA encoding成的语言是decidable的
  - 找找看有没有通向接受的路径：广度优先算法
  - 不能输入所有的字符因为那可能不会停下
  - 其实这里也可以说最长是状态数？但是要证明一下，有点麻烦
- 证明 两种含有相同language的DFA encoding组成的语言是decidable的
  - 也可以说 输入一定长度的字符串 因为可以知道最多是两个有限自动机的状态数的乘积
  - 建造一个C有限自动机=A和B的自动差，然后就回到了第一个问题，用检测第一种的方法检测
- 证明\<G,w>是decidable的，注意这里G是上下文无关文法
  - 先把G转换到CNF上下文无关文法
  - 尝试所有2w-1的转换（因为如果有这种那么它就应该是2w-1的）
  - 如果产生了w就accept，否则reject
  - 这样就可以推导出：每一个上下文无关语言都是decidable的，因为每个上下文无关语言都可以构造出一个上下文无关文法，然后根据上下文无关文法构造出上面对应的\<G,w>turing machine
    【chomsky normal form/CNF】只允许两类规则
- A->BC
- B->b
  【lemma 1】总是可以将一个上下文无关文法转换为一个CNF
  【lemma 2】如果H是一个CNF然后w是L(H)的一个字符串，那么每个w推导出来都有2|w|-1个步骤
- 一种语言：满足 语言集合为空集的 上下文无关文法解析成的语言 是否是decidable的
  - 把每种terminal都标记
  - 对于一个derivation右边所有字符都蓝了的，那么左边也标记
  - 如果最初的符号也被标记了，那么说明这个语言可以推导出language
- 一种语言说明两个CFG是不是相等的：是NOT decidable的：因为CFG之间在补集不闭包，所以没法按照DFA做对称差来进行判断
- 判断一个TM是否接受字符串w的语言是not decidable的；但是是recognizable的
  - 在类推recognizable的时候遇到了问题，因为很难判断一个TM是never halts的还是只是运行了很长的时间（这好像也没说怎么解决啊 就不管了？）

# undecidability

## 不decidable的例子&康托对角线

哦判断一个TM是否接受字符串w的语言是not decidable的，如上所讲

- 【proof】假设有一个对应的TM可以判断$A_{TM}$，先反证假设有一个可以判断是否decidable的TM叫H，利用H构造一个D，令input为\<M>，然后对\<M>运行H，即H上运行\<M, \<M>>如果H接受那么D就拒绝，否则则反之
- 这个时候令M为D（因为D也是M啊！一种turing machine（哦对这里M就是随便一个TM））然后就发生矛盾了，D接受\<D>当且仅当D不接受\<D>，这里不同的$M_i$和$<M_i>$构成了一个二维表格，其中在D这个row里面D第一列和M1不一样，第二列和M2不一样…第D列的时候不能和自己不一样，所以就矛盾了
- 为什么不是构造失败而是假设失败 感觉构造失败也是合理的？类似于有些集合本身就是不能被构造出来的这种，不能随便套在一个命题上说这个命题是假的（吧
- 哦因为这里D其实不是自指涉的，就是已经构造出来了一个H再根据H去构造出来一个D，所以没啥关系，芝士合理的！哦这里老师承认了有一点自我指涉（
- 康托对角化原理来了
- injective：就是函数应该符合的条件；subjective应该意思是逆过来对应
- countable sets：可以将集合和自然数集合N一一对应的集合；即可以排列成列
- 哦死去的知识 数分和离散数学ovo
  【corollary（必然结果）1】：L=all languages：L就是uncountable的
- 【proof】：对于所有的字符串（这是可以排列的）排成一整串，把有对应字符串的位置记成1，没有的记成0，排列出来一个二进制小数，所有的语言组成一个R一一对应，那就是uncountable的
  【corollary 2】：所有的turing machine组成的集合是可数的
- 【proof】哦我懂了 因为图灵机是可以用一个有限长的字符串描述的（形式化描述），所以所有的turing machine可以归结于{0, 1}\*组成的集合之中的子集，而前者就是countable的，那么turing machine组成的集合当然也是countable的
- 关键在于TM是可以描述的！
  这样可以说明 有大量问题是turing machine无法解决/判定的、
  there are more languages than TMs总有一些语言是无法判定的
  axiom：公理

## TM补集是T-unrecognizable的

### A和A非都是recognizable的，那么A就是decidable的

令M1和M2辨认A和A非 尝试找出一个可以decideA的TM
令T=对于一个input w

1. 先平行运行一下M1和M2，直到有一个机器accept了w
2. 如果M1accept了那T就accept，如果M2accept了那么就reject
   ->==这一下子就推出来TM的补集一定是unrecognizable的！因为如果是recognizable的那么TM应该就是decidable的，但是不是==

## reducibility方法

使用TM是undecidable来推出其他也是undecidable的
【halting problem】是否TM M会在w上停机？的这个语言是undecidable的

- 哦尝试证明A\_TM是可以被HALT\_TM推导出的，这样反证就成功了
- 假设有一个R可以decide HALT，尝试构造一个S
- S=对于一个输入w，对R测试是否M在w上停机，如果答案是否定的，那么就拒绝w
- 如果确实会停机那么就在M上运行w，M接受就接受，M拒绝就拒绝
- 这里S是decidable的！那么这就说明了ATM是decidable的，这是不对的，于是反证出停机问题是undecidable的
