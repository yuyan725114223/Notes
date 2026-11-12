---
publish: true
created: 2026-09-29T05:26:52.928Z
modified: 2026-11-12T10:31:35.378Z
---

操作数可能在：指令编码/寄存器/内存
操作数大小：要存的数据的大小；
地址大小：访问内存的地址宽度的大小
学习一下x86汇编（保留双操作数的模式）
几种模式：

- 保护模式
- 实模式
  【三种操作模式】默认的地址/操作数大小
- 【16位】real，vm86, protected
  - 实模式一个段有64KB
- 【32位保护】
- 【64位】==注意64位模式下默认的操作数大小依然是32位，如果想要操作64位数据则需要添加REX前缀==

# 指令组成

【label】

- 必须是字母/特殊符号开头@, \$, -, or ?
- 长度有限制1-35bit
  ；后面会被自动当成注释

## 两种语法

【AT\&T】

- 数据传播方向左到右
- 立即数前\$，reg前%
- 访存：disp(base, index, scale)
- 指令后缀：b, w, l and q specify byte, word, doubleword and quadword
- 数字格式
  1. BIN：0b100
  2. OCT：04
  3. DEC：4
  4. HEX：0x4
     语法更冗余 指令更清晰
     【Intel】更多的编译器支持：
- MASM, NASM, FASM, TASM, YASM
- GAS with .intel\_syntax
- IDA Pro
  更加直观 不太需要额外的提示消息

# 寻址模式

确认操作数的地址

## 数据寻址

不明确的没有明显寄存器提示指令操作位数的需要通过PTR来显式指示多大

- 指针只用来把index reg指明的立即数/指针加载到内存里面
- 多数据SIMD还会使用OWORD表示128b数据

### 寄存器寻址

分类

- 8b寄存器：AH, AL, BH, BL, CH, CL, DH, DL
- 16b：AX, BX, CX, DX, SP, BP, SI, DI
- 32b：EAX, EBX, ECX, EDX, ESP, EBP, EDI, ESI
- 64b：RAX, RBX, RCX, RDX, RSP, RBP, RDI, RSI, and R8-R15（64位加进去的寄存器，64位以下都不会用到）
  这里注意上面的寄存器只是下面的寄存器截取了一部分进去
  如果两个操作数都是寄存器，那么这两个的宽度应该一样，不然会报错

### 立即数寻址

- 如果有一个立即数，那么长度以内存为准，多的清零
- 有的编译器会在前面加#表示即将进入立即数
- 如果十六进制的立即数开头是A-F，那么前面应该加0，以防误认为label变量，非字母开头也可以加
- 16进制加H，2:B，10:不加
- 加引号表示是asc码或者字符
  - asc码在寄存器之中是反向存储的

### 内存寻址

有效内存地址：四个寄存器/立即数的参数混合
\=Base+(Scale×Index)+Disp
都要先加一个段地址啊
==默认段的挑选：如果有BP系列寄存器（BP, EBP, ESP）则使用SS段基址，其它都选择DS==

- 如果在实模式下使用了32位寄存器，实质的数据内容大小也不能超过16bit
- 保护模式只要不超过有效地址位置就可以随便用
- 64位不参与地址计算，统一认为基址0，寄存器内即位完整的线性地址
  ==哦这里disp偏移量和scale放缩比是固定的，但是index是可变的变量==

#### 64位

真的64位地址实现起来太大了，一般会用48/57位有效地址，使用标准形式：
都设置成0或设置成1，等价于符号扩展

- 如果有不一样的扩展位就会引发GP（general-protection exception）
- 避免其它的bit被用来用作其它
- 可以跑不改变的长模式 支持更大的虚拟地址的实现
  - 意思是地址空间升级对软件透明，如果本来就是符合规范的设置，地址扩展之后依旧好用

#### Direct Data Addressing (Disp)

##### 直接寻址

mov：reg，mem
其中mem就是\[直接的地址标号]或者等价label
太常用了所以专门设计了一个又短又快的指令：

- 只有mov指令并且目的寄存器是A系列寄存器的时候能够享受到这个待遇
- A系列accumulator最经常用到，需要快捷道路
  编译器在静态地址（全局变量，在编译时候确定的数据）加载的时候使用该条指令

##### 偏移量寻址

基本一样，但是指令长一个字节，一共4个字节
基本上所有的指令都会用这个

#### Register Indirect Addressing (Base)

不允许除了字符串指令之外的 内存-内存的传输
经常用来访问内存里的表格数据？

#### Base-Plus-Index Addressing (Base + Index)

index表示一个矩阵里的偏移相对位置
80386之上可以使用任意两个32bit寄存器作为base/index
MOV DX,\[BX + DI]也会写成MOV DX,\[BX]\[DI]
用来访问内存阵列里的元素

#### Register Relative Addressing (Base/Index + Disp)

用来访问structure
![[图源/图源2/Pasted image 20261112144316.png]]
这里是因为传进来的参数会先被放在栈顶

#### Base Relative-Plus-Index Addressing (Base +  Index + Disp)

访问二维数组的地址
一般不太用，因为太复杂了

#### Scaled-Index Addressing (Base+Scale×Index+Disp)

scale：固定的1 2 4 8
默认的1不需要被提及
访问一个数组里面的structure
如果是8086-80286的话需要base BX/BP, Index SI/DI, DISP随便8/16b

#### RIP相对寻址

【RIP/IP/EIP】存储下一条要执行指令的地址的寄存器
相对指令指针寻址：用指令自己当前的位置作为基准，一小块代码一起偏移，无需重定位
base+disp，不过这个base是RIP而不是通用的寄存器
32位寄存器符号扩展到64位再加RIP

## 栈寻址

可以push/pop六种形式

- reg，mem，imm
- 段寄存器，flags，all reg（意思就是所有寄存器用一条指令一次性压进去了）
  ==pop没有imm，因为pop后面跟的 意思是把pop出来的栈顶元素放到这个里面；也没有CS，因为一句指令就能够改变代码运行位置权利太大了，没有设置这种检查==
  一个字的数据存到栈顶：
- 高八位：SP-1
- 低八位：SP-2
  内存段：放在PSP，在每个程序文件的开头
  如果不定义栈的话，会把PSP所在的段当作栈来使用
- 如果栈一直增长但是上面的空间不够了，需要使用更多的栈内存，需要擦除PSP里面的信息，PSP中的信息就会被数据冲掉，然后就该报错了
- 【PSP】包括：命令行参数、环境变量指针、DOS用于管理该程序的内部数据（程序退出时需要恢复的现场）
  ==栈天性上都是循环的，top上的位置和bottom连续==
  ![[图源/图源2/Pasted image 20261112171350.png]]
  这里因为要先-2，所以会跑到上面去，这里展示没有执行之前，执行完之后1FFFE也会有A037
  16bit：pusha这里的SP是更改之前的SP，为了保证前后栈指针的一致性
  pop的时候有固定的顺序：DI, SI, BP, SP, BX, DX, CX, and AX
  32：pushad
  这两个8086/8088的处理器之前都不兼容

### 初始化一个栈

```masm
STACK_SEG SEGMENT STACK  
	DW 100H DUP(?)
STACK_SEG ENDS
```

如果没有初始化栈的话在链接的时候会弹出一个警告

## 代码寻址

### 两种偏移

- 相对偏移：相对当前代码指针的位置（IP）
- 绝对偏移：和代码段相比的绝对偏移

### 四种跳转

- short jump：1B
- near jump：2B
- far jump：直接程序地址寻址，也可以使用far ptr
- task switch：跳到另一个任务的指令里

### indirect 程序内存访存

两重：先到对应的寄存器-内存访问到第一个指针，再根据这个指针跳转到真正要跳转的地址位置
_JMP NEAR PTR\[DI+2]_

# 杂项

- 可以用.model来定义文件的开头/内存模型
- 如果是TINY大小，程序就在代码段里，并且被编译为一个command程序(.COM)
- SMALL模型：用一个代码/数据段+汇编 一起做一个exe可执行程序
- ==ESP和EIP都不能做base/index寄存器==：EIP不是通用寄存器；ESP能当base不能当index
