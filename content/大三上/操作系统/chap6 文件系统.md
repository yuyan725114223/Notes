# 接口
文件系统+目录结构（提供权限信息？）
文件时逻辑外存的最小分配单元，即数据只有通过文件才能写到外存
- 文件属性信息是目录结构的一部分
## 文件操作
【repositioning with a file】将当前的文件指针重新定位到定值，如开头或者结尾
【truncate】清空文件内容但是保留文件属性
【open】返回一个handler用来执行操作
	【打开文件表】记录哪些文件被打开了
	【file-open count】被多次打开的文件被多次打开的数目
## 文件锁
【共享锁】r lock
【独占锁】w lock
【强制锁定】其他任何进程都不许访问
【建议锁定】如果已经知道锁了但是还是想访问那就访问吧
## 文件类型
～文件扩展名/在文件的开头放一点Magic number
- 或者直接不识别文件类型，先按照想开的方式开了再说；操作系统保障不会出现什么太坏的结果
文件结构可以有可以没有
## 目录结构
单级目录：比较重要保存location
二级目录
【UFD】用户文件目录
【MFD】主文件目录，不同的ufd聚合而成
树级目录：每个文件放一个bit来表示这个文件是文件还是目录
删除的时候把
1. 子文件的直接都删了
2. 非空不许删
无环图目录
【link】指向另一个文件/子目录的指针
【符号链接/软链接】可能会有dangling pointer
	- 不管，用到了再告诉
	- 维护一下指针，删的时候把指针也删了
【硬连接】把所有的文件信息都复制了一份
【引用计数】每个文件在被创建一个新的硬连接的时候计数+1，删除-1；计数为0的时候文件本身可删除
==hard link不许引用目录，但是soft link可以引用目录==，因为hard link可能会有环然后这样就增加了成本
- .指向自己的hard link，..指向父目录的hard link
- hard link不能跨越file-system boundary，因为system之间fcb标识号不一定唯一；soft link可以
### 通用图目录
允许目录中有环
- 遍历&删除比较麻烦，因为可能会相互/自我引用导致没有引用的时候引用计数也不为0
- 【垃圾回收】确定哪些文件可以删除
【volume】【挂载】到文件系统的某个位置，这个位置称为【挂载点】
【ACL】指定每个用户&访问类型
类别：所有者权限；所属者权限；其他人权限
root用户：
- 可以读写未规定读写bit的文件
- 可以进入未确定x bit的目录
- 如果3个x都没有，那么为不可执行文件，就算是root也不行
# 实现
文件系统和os可以如何交互 包装成一个容易交互的文件系统、提供高效便捷的磁盘访问，将这种逻辑结构映射到物理的外存设备上
专用文件系统（google 搜索）分布式文件系统
## 系统结构
分层：可维护性高一点，降低文件系统的复杂性和冗余性，当然解耦之后性能可能降低
![[Pasted image 20260827105702.png|200]]
- application programs：把指令转换成read/write/open某个文件路径的指令传给下一层
- logical file system：管理metadata（额外信息）
	- 【FCB】一中保存这些信息的数据结构
	- 把读写开文件路径的指令转换为读写某些logical blocks，并传给下一层
- file-organization module：逻辑到物理实地&管理free space，跟踪分配未被使用的blocks
- basic file system：包含一些缓存，类似于cache；如果上层读写没了就传给下一层
- io control：设备驱动程序&中断处理程序，主存和磁盘系统之间传递消息，上层转化为低层级，硬件的指令来实现
## 数据结构
disk上的structure
- FCB：保存一些文件信息，每个fcb有一个唯一的标识号和目录条目相关联
	- unix中被称为inode，NTFS中是master file table中的一行
- boot control block：通常是volume的第一个block
- volume control block：当前volume的一些信息，ufs称为super block
- directory
memory里的structure
- mount table：记录了挂载了哪些卷
- directory cache
- global open-file table
- per-process open-file table
- buffer用于读写磁盘
system-wide open-file table：存储打开该文件的进程的数量
per-process open-file table保存指针，当前进度&访问模式etc
【file descriptor/file handle】打开文件的时候第一个对应的entry
操作不需要文件名，但是缓存文件名打开同一个文件的时候快一点
## 【VFS】虚拟文件系统
提供给上层一个比较统一的接口调用，下面接了不同的文件系统
目录：保存file name到FCB的映射关系
用线性结构或者哈希表
## 磁盘block分配策略
连续分配
	文件内容可能是可扩展的，难以确定到底有多少？
	出现这种情况的时候将文件复制到一个空闲空间中
	or添加一块连续空间，维护当前+下一个的extent信息
linked allocation：保存一块链
	如果指针坏了的话容易出点问题
	可能使用 比如 双向链表来改善
	总表里面只存储开始和结束的地址
indexed allocation：加一个索引块来记录每个应该对应的地址
	index block也链表一下
	多级index block连接一下
combined scheme：前12个指向普通的block，第13个指向一级的索引块，第14个指向二级的索引块，第15个指向三级的索引块
## 空闲空间管理
bitmap：每个block用bit标识，1空0占用
把所有的free space用链表连接，在需要多个空间块的时候需要比较多的io，因为要一个一个找
【grouping】每个block里面保存空闲块的地址
【counting】每个空闲块维护空闲的指针和空闲的连续大小