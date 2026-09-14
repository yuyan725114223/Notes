# 同步
## why 同步
【race condition】防止出现竞争状态，同一时间只有一个进程可以操控某一个变量
	kernel code应该也有很多race condition
	两个fork可能会请求到同一个pid，那就挂了
【协作进程】可以影响别的/被影响的进程；共同使用同一份数据就可能导致data inconsistency
## 问题建模
【critical section】可能修改和别的进程共用的数据的代码
==系统需要满足：当一个进程正在运行它的critical section的时候，其它进程都不能进入它的critical section==
要设计一种安全共享数据的协议
	规定进程所做的工作之间的顺序/先序关系，防止一些非法情况的发生
【entry section】即将进行critical section的代码，进入critical section有一些条件
【critical section】核心代码
【exit section】要尽快地把这个锁还回去
【remainder section】剩余的不重要的代码
【需要满足的要求】
1. 互斥的，没有两个进程可以同时在运行cs
2. progress：整体上系统是在运行的，只能是
	1. 正在cs
	2. 即将cs
	3. 没有人要cs
	不能即没有人在/要进cs又有人在等cs，这就不对了，有进程在摸鱼
3. bound waiting：等待进入cs的时间是有限的，只会等待有限个线程先运行
单核系统在critical section禁止中断（但是可能危险，因为这样权限太大了）
【抢占式内核】是否允许kernel mode的进程被抢占；响应更快，性能更好，但是比较难设计
【非抢占式内核】保证一个时间点只有一个进程能够在kernel里面跑
## peterson solution
![[Pasted image 20260825110615.png]]

证明：
【互斥性】
![[Pasted image 20260825110724.png]]
【progress】要么有一个是在cs，要么两个都不在cs，要么有一个即将进入cs
【边界等待】那么剩下的代码必然是有限的，因为一个在循环的话必然另一个在cs，那么没有cs则剩余有限
现代编译器/处理器重排之后可能会有点问题，比如可能交换之后两个节点都同时进入cs，就挂了
【Memory barrier】大概就是隔一下来保证实现的顺序，保证有一些地方不能重排&访问到的东西是比较正确的东西（内存里的改变对其他处理器上的进程是可见的）
【memory model】强顺序：保证看到的一定是最新的顺序，写through；弱顺序
## Hardware Inst
本来是抢到锁的人不waiting，但是有可能一直抢不到锁。后面改成锁优先传给顺位的下一个人
执行是atomic不可分的
【test_and_set】拿到锁之后，返回当前的值，然后马上设成true，这样就仅有一人会拿到锁（？
【compare_and_swap】返回原值，如果value=预期值就用新值代替；和上面的差不多，但是更加方便&功能丰富一点，比如可以设个档
## Atomic var
原子性的更新，如使用不可打断的（&increment）代替count++
只能解决变量更新过程中的race condition，不能解决所有的race condition
## Mutex
就是互斥锁
希望require()和release()都是原子化的操作
如果争抢锁的进程太多的话效率就不是很高
【contended】被争夺的锁
也可以换成跳出到其他的进程，但是如果context switch代价太大的话不如spin lock自旋比较划算
## Semaphores
P操作：wait()
V操作：signal()
这俩应该都是atomic的
binary：类似于互斥锁
counting：任意整型都🉑
【waiting queue】解决busy waiting
不当使用可能会导致死锁，因为可能都在互相等对方
## 【priority inversion】
优先级中间的进程抢占了优先级最低的进程导致优先级最低的进程没有办法把锁还给优先级最高的进程，相当于优先级中间的进程事实上优先级高于了优先级最高的进程
这样就导致了事实上的死锁
【优先级继承】正在访问资源的进程获得需要访问这个资源的更高优先级进程的优先级，比如上文低级进程可以暂时获得要访问这一块的高优先级的优先级，意思就是暂时变成高优先级的进程