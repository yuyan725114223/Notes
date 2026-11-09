---
publish: true
created: 2026-10-27T05:26:12.479Z
modified: 2026-11-09T11:52:59.552Z
---

# 加

==不允许内存\&segment之间的加法==
【inc】也不能用在segment上；==不动CF！==
不能确定到底数据多大，所以需要加一个指针明确指定一下，不然就会直接加1
must be described by using the BYTE PTR, WORD PTR or DWORD PTR directives
【adc】可以用来扩展进位bit数
因为依赖太多无法并行：引入新的变量【ADCX/ADOX】如果有两串大数相加都需要进行计算，一组使用carry-bit作为进位，一组使用overflow-bit作为进位，包装成的加法指令一组叫做adcx，一组叫做adox
【lea】只做地址计算，把算出来的地址当作普通数据存入寄存器
【xadd】先交换des和src的内容，然后把和防盗des里面去（少见的会改变源寄存器的指令）
源寄存器只能是reg，目的寄存器也可以是内存
多核/处理器可以加锁
天然可以用来分发票据 保证分到的数字是唯一的
适合用来【乐观锁】不加锁直接改，提交的时候验证版本，适合冲突少的场景
【悲观锁】每次都先加锁独占资源，没抢上的排队等，适合频繁冲突的场景

# 减

【sbb】有borrow的减法，和加法的carry一样
【dec】不管cf，但是其他都还是改的！不能当作什么都不改
==所以一般循环计数都不用这个加减==
条件跳转也可以用吧毕竟只需要最后的flag对了就好了
【CMPXCHG】：比较&交换，比较目的寄存器和accumulator（隐含的寄存器，是AL/AX/EAX），如果相同就把目标寄存器设为源寄存器，0标志符置1；如果不相等就把eax设成des，zf设成0
可以进行原子化操作，并且一般会加一个LOCK来确保这点
要返回目的寄存器在改之前的值
【8B】默认认为EDX：EAX为old value，ECX：EBX为new value
类似操作系统里面的竞争锁

## flags

### AF

辅助进位标志：低四位之间是否有进位/退位

### PF

奇偶标志：反应低8位中1的个数是奇数还是偶数（偶校验）
一般用于串行通信的奇偶校验检错

# 乘法

需要分类有无符号来确定乘法结果扩展之后高位应该填充符号位还是0

- 有隐含操作数AL/AX/EAX
- 结果都是两倍的源操作数
  八位
- 不允许直接立即数，需要使用单独的IMUL指令
- 结果AX
  16位：结果DX-AX
  32：EDX-EAX
  64：RDX-RAX
  【IMUL】（有符号）
- 一操作数：和之前的一样
- 两操作数：可以使用立即数相乘；乘积结果被截断了到16位；可能会丢精度
- 3操作数：后两个数乘积结果截断然后存到第一个数当中
  - 其实双操作数在汇编的时候会变成3操作数形式，所以这两种其实共享同一种opcode
  - 无符号：使用OF和CF来判断高位是否包含有效信息
  - 有符号：比较乘法结果的符号和符号扩展之后放在寄存器里面的数
  - 应该test一下CF/OF来确定没有重要的数据没有了
  - 为什么IMUL的2/3操作数会截断：因为两倍的精度结果是做高精度科学计算，要求精度；普通的计算比较关心高位结果，所以就截断了
    2/3操作数的时候源操作数都可以是立即数

# 除法

有符号（DIV）/无符号（IDIV）
==没有立即数除法==
两种错误：

1. 除0
2. overflow，如果一个大数除一个小数
   8位：被除数放在AX
   16：DX-AX
   可能会被MOVZX来0扩展
   32：EDX-EAX
   64：RDX-RAX
   商放在低位，余数放在高位
   【round-toward-zero】舍入向0靠近：更靠近0；被除数和余数的符号一致
   被除数必须使用寄存器，除数可以使用内存

## 符号扩展方式

- CBW/CWDE/CDQE：扩展一倍
  ![[图源/图源2/Pasted image 20261109113238.png]]
- CWD/CDQ/CQO：复制rAX的符号bit到高位的所有bit
- MOVSX/MOVSXD：先复制内容，再源寄存器符号扩展到所有的目标寄存器
- MOVZX：0扩展

## 余数处理？策略：

不管
四舍五入：余数乘2和除数比一下看看是否达到了一半来四舍五入；注意这里乘2的时候要考虑carry-bit，不然会得出错误的结果（最高位的1被截掉了）
继续往下除：商拿掉填0再除出小数
🌰：
![[图源/图源2/Pasted image 20261109115303.png]]

# BCD

都用AX作为源&目的寄存器
==64位都不兼容==
DAA：先做加法，做完加法之后整理
看看有没有AF auxiliary carry（half-carry：如果有就要+6）
如果调整完之后超过了2B就要调整CF carry flag
DAS：BCD码的减法
放在减法后面做调整

# ASC2码

也都用AX作为源&目的寄存器
【unpacked bcd】一个字节只放一位十进制数

- AAA：ASC码加法
  先加，加完之后高4位强制清零，低四位如果有非法字母按BCD的方法类似处理（+6），然后把AH/CF/AF值1
  因为有8位，所以清理高四位没关系；相当于低位加法的进位直接放到AH里面去，而清理的是AL的高位
  implied：AL
  AH初始必须是0
- AAS (ASCII adjust after subtraction)调整AX
- AAM (ASCII adjust after multiplication)
- AAD (ASCII adjust before division)

# 基本逻辑指令

最右边位置的bit是bit0

## and

可以用硬件/软件（CPU指令）实现，如果不是特别要求时间，可以使用CPU AND（更加灵活）
不能是 两个mem/有段寄存器（只能用 MOV、PUSH、POP、LDS 等少数指令访问，算术/逻辑指令一概不碰它）

- 用来掩码

## or

逻辑加法
也叫同或

### 异或

comparator
用来反转固定bit的位置（设定为1）

## test

使用and但是只影响了flag reg
源寄存器通常跟立即数test
test eax eax和cmp eax 0是一样的，但是后面这个编码之后长一点，所以这里用test更好
![[图源/图源2/Pasted image 20261109143153.png]]
这里是根据结果是不是0来判断是否equal
test eax，eax：判断eax是否<0
![[图源/图源2/Pasted image 20261109144407.png]]
四条：把对应位置的数复制进到CF并进行指令对应的操作
sar：算术右移
BSF/BSR：位扫描指令，可以高-低/低-高

## not

不改变任何的flags
所有bit都取反：逻辑取反

## neg

算原来数的2进制补码，算数取反
如果操作数是0 cf就是0，否则cf是1，其他的flags都根据计算结果设置
编译器运用小🌰
![[图源/图源2/Pasted image 20261109151517.png]]

# shift

底层软件经常用来控制io，微处理器有完整地实现架构
等价于乘除2的整数倍
4种，算数&逻辑：SHL/SAL/SHR/SAR REG/MEM, Count

- 移出去的bit会先到CF里，然后被丢弃
  SAR的除法商会倾向于被归约到-无穷，但是IDIV倾向于会被归约到0（所以余数倾向于变成正数）
  如果需要倾向于0的话需要做一个舍入（+2^k-1）
  【SHLD reg1, reg2, imm8】先把reg1和reg2连接起来再左移imm8
  ![[图源/图源2/Pasted image 20261109162049.png]]
  注意是第二个操作数是源操作数，第一个操作数是目的操作数

## 循环移位rotate

ROL/ROR/RCL/RCR REG/MEM, Count
RCL/RCR会把CF标志符移进least-significant bit
![[图源/图源2/Pasted image 20261109162919.png|382]]
循环的个数可以立即数/CL
![[图源/图源2/Pasted image 20261109163325.png]]

# bit扫描

BSF：从右到左
BSR：从左到右找1-bit
如果找到了就把1的位置放到目的寄存器里
【TZCNT】(trailing zero count)尾巴上有多少个0
【LZCNT】(leading zero count)头上有多少个0
同时下两个指令CF=ZF都会被设置

# 字符串比较

指内存中连续的一串字节/字/双字，配合REP前缀可以自动重复执行

## SCAS

使用D来选择自动的增加/减少
和AL/AX/EAX比较，比较结果只改变EFLAGS中的状态标志
内存地址固定从ES:EDI读取
counter一般放在CI，使用CLD设置auto-increment
如果找到了就直接退出，CI上存储了剩余需要再比较的次数

## CMPS

比较SI/ESI的内容和DI/EDI的内容
一般和REPE/REPNE/REPZ/REPNZ连用

# 增量/减量

不影响CF，因为一般用来循环，害怕干扰比较
