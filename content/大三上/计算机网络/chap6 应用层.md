【DNS】映射表，记忆ip地址和便于记忆的主机名之间的映射
- 加.表示绝对地址
- 拥有域名的可以给下面再分配一些域名
- 迭代查询，主要本地name server解决domain的name解析问题
- ![[Pasted image 20260819173912.png]]
- query和response使用UDP发的，因为不重要，如果失败就重新发一遍or换一台name server就好了
- 二进制存的
【Email】
- 【MIME】加了一些非asc2码发送的过程
【SMTP】方便调试的ASC2协议
![[Pasted image 20260819203242.png]]
不同的请求方式
![[Pasted image 20260819203450.png]]
【ftp】
- 【控制进程】port 21
- 【发送进程】port 20
有两种方式
【post】client开个端口N给server发post N；但是如果client有NAT可能就连不上了（没有注册过 不知道谁是谁）
【pasv】server随便开一个端口然后告诉client连接这个端口，配置比较复杂而且安全性不高