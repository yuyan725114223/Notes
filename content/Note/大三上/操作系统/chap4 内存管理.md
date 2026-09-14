---
publish: true
created: 2026-08-26T04:01:33.879Z
modified: 2026-09-14T11:08:52.449Z
---

# 主存

内存管理？
保护内存空间，防止用户程序修改操作系统或者其他用户程序的代码or数据
大部分的时候地址都是相对地址 编译器绑定之后变为绝对地址
一种好想的方法：

- protection
- 不能因为保护降低访问内存的效率
- 进行context switch的时候对执行的影响不大
  内存不够的时候？

1. 直接报错
2. waiting queue待命
   划分方式？

- 固定partition大小的时候浪费较多，有internal fragmentation，一个partition只能有一个进程
- 【外部碎片】使用动态的划分，之间可能有很多的空隙但是利用不起来；加起来可能就够了但是分开比较碎，因为不属于任意一个进程所以叫外部碎片
  三种嵌入方式，应该是ads的装箱问题学过
  protection：设置一下base和limit，在这之间的才可以通过然后正常访问，否则会报错
  使用segment和offset来定位地址，这是虚拟地址
  - 通过segment定位到segment register table里面的表项，如果offset>=limit就会错误，物理地址是base+offset
  - table里有权限位，RWX，完成对系统的保护
    【MMU】内存管理单元，完成虚实转换
    【compaction】移动内存中的内容 重排是的所有空闲的空间连续，但是比较耗时
- 想到 是否可以让程序不再需要连续的地址？
  【paging】page是逻辑地址分页，frame是物理地址分页；用逻辑地址的连续代替物理地址的连续
  【PTBR】页表放在内存里面，然后PTBR指向页表，但是这样就需要两次访问？！时间更长了
  【ASID】每个条目中保存，标识唯一对应的一个进程
  如果用TLB的话，平均每个多1-r
  【PTLR】保护，标识可以访问的最大地址位置，防止访问了不改访问的出错
  【reentrant code】可以共享代码，要求是跑的时候不能改自己
  【多级页表】本来的太多了，决定使用一些比如再分一层，这样对连续性的需求有所降低；可以多分几层也可以直接不管最高的几个位
  【反转页表】用每个physical页表对应到logical页表，只需要一个page table，但是对应到的每个logical地址都要搜索一下
  【swapping】类似于LRU的替换思路

# 虚拟内存

swapping之后内存扩展了
【flat memory】分段/分页之前各进程&操作系统共同使用同一个地址空间，碎片比较大&隔离性比较差
隔离之后每个进程有自己的逻辑地址空间

- 只要mmu不出现问题&页表不被篡改，那么这个进程就是隔离的
  exception可能性

1. 访问错误地址
2. 没有权限
3. 合法，但是对应的page已经被swapped out了
4. lazy allocation已经请求了，但是os还没有分配
   1，2就杀掉进程，3就换回来页之后唤醒进程

- 分成kernel portion和user portion；其中kernel mode都可以执行，但是user mode只能执行user portion的代码
- 【AS】虚拟地址空间
- 每个进程AS的kernel portion都映射到了同一块物理内存
- 一半默认来说kernel的代码都放在上面1GB/用户3GB
  【TTBR】页表的基地址
  0：用户地址
  1：kernel地址

## demand paging\&lazy allocation

【lazy allocation】用户申请一块内存的时候不会立刻分配，而是在内存真的被访问的时候分配
【RSS】进程在内存中占用的空间大小
【brk】希望增加heap内存分配的指令

## copy-on-write

允许父进程和子进程最初使用同一份物理页，在要写入共享frame的时候才复制

- 只拷贝映射关系
  【vfork】调用之后父进程就被挂起了，子进程接管父进程的地址空间然后在里面写；父进程回来之后都能看到
  【Belady's Anomaly】FIFO可能会倒挂，物理帧增加的时候page-fault反而变多了

### lru approximition

用一个reference bit，当一个page被访问的时候把reference bit置为1，同时定期把所有的rb清零

## 帧分配

最小不能小于单个指令操作所需要的frame数量
【全局替换】每次置换的时候从所有帧里面选取一个帧计算
局部替换：提前分配物理frame的资源

## 抖动

【thrashing】如果可用的帧数量比较少就会频繁出现page fault，此时调页时间比较长，因为会被频繁地换出换进
【工作集模型】保证频繁访问的页面组里面的页面不被换出，如果当前频繁访问的页面数>剩余可用的，就把整个进程都换出来，等会儿再处理

## kernel memory allocation

很多数据结构的大小差的很多，所以应该尽可能的让kernel减少碎片；需要比较连续的物理内存，想要减少开销

### buddy system

每次分配的内存大小只分配2的幂次方，释放的时候相邻就可以合并成更大的块

### slab allocation

对于一些大小比较固定的变量，可以切出来比较完整的一块然后拆掉frame的格子来放这些数据变量
