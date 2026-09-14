---
publish: true
created: 2026-08-26T11:14:54.727Z
modified: 2026-09-14T11:08:52.451Z
---

# 大容量存储

【HDD】硬盘驱动器
【NVM】非易失性存储容器
其实NVM应该是包含硬盘的，但是一般都说包含电子的不包含机械的，所以就不算了
\- 更可靠&贵，但是快很多
【RPM】每分钟旋转的次数
磁带：空间很大，但是随机访问特别慢

## disk scheduling

磁盘io的时候向os请求：输入输出、磁盘地址、内存地址、扇区数
规划一下下一个使用目标磁盘是什么？
【SSKF】优先访问离现在head position最近的request，但是不一定是最好的
\- 可能需要算很多东西
\- 可能有些会饿死
\- 变化比较大 不确定
【电梯】可能如果访问的地方一直都比较近表现就不好
【C-SKAN】只走到一端最后一个任务 看看是否有新的请求，没有就回头
如果优化了让访问的地方都比较近就会表现好

## disk management

### 磁盘格式化

【低级/物理格式化】比如头信息，ecc等

- 分区，视为很多个logical disk
  【逻辑格式化】将厨师的文件系统数据结构存储到磁盘上来创建文件系统
- 预留一些block来handle bad blocks
- 将若干blocks聚合成cluster让磁盘io单位为block，文件io单位为cluster，减少random access
  【卷/volume】包含文件系统的分区

### boot block

【自举程序/bootstrap】刚打开电源/重启初始化系统的程序

- 一般在ROM中，只读，so不受病毒影响
- 用tiny bootstrap loader program更改bootstrap的代码
- 包含的磁盘成为boot disk/system disk

# I/O

【polling/轮询】pc要和io设备交互的时候一直问controller好了没
【中断】～计组
【DMA/直接内存访问】大量传输的设备上面两种占的都有点多，这样设备直接与内存交互
【DVMA】DMA使用虚拟地址的技术

## 应用程序io接口

隐藏具体的设备的区别
按不同的需求 区分一些特性偏好设置，形成一些小的分类
【文件描述符】操作系统确定一个设备的key attribute
