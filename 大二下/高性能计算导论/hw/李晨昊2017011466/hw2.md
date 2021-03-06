## Q1 当讨论浮点数加法时，我们曾简单地假设每个功能单元都花费相同的时间。现在，如果每个取命令与存命令都耗费2纳秒，其余的每个操作耗费1纳秒。
### a.在上述假设下，每个浮点数加法要耗费多少时间？
有两个耗时2ns的指令和5个耗时1ns的指令，总共耗时9ns

### b.非流水线1000对浮点数加法要耗费多少时间？
9ns * 1000 = 9000ns

### c.流水线1000对浮点数加法要耗费多少时间？
2ns * 1000 + 7ns = 2007ns

### d. 如果操作数/结果储存在不同级的内存层级上，那么取命令与存命令所要耗费的时间可能会差别非常大。假设从一级缓存上取数据/指令要耗费2纳秒，从二级缓存上去数据/指令要耗费5纳秒，从主存取数据/指令要耗费50纳秒，当执行某条指令，取其中一个操作数时，发生了一次一级缓存失效，那么流水线会发生什么情况？如果又发生了二级缓存失效，又会怎样？
这里假定读取耗时已经包括了在上层的存储结构中的查找过程的时间

一级缓存失效时：5ns后下一次读取才能开始，而7ns后上一次加法才结束，因此任一时刻还是有>=2次加法在执行，可以认为流水线没有中断

二级缓存失效时：50ns后下一次读取才开始，因此存在43ns的时间CPU只在等待这一次读取，可以认为流水线在这里中断了

## Q2 回顾之前一个从缓存读取二维数组的例子(见第2.2.3小节)。请问一个更大的矩阵和一个更大的缓存是如何影响上面两段嵌套循环程序的性能的？如果MAX=8，缓存可以储存4个缓存行，情况又会怎样？在第一段嵌套循环程序中对A的读操作，会导致发生多少次读缺失？第二段嵌套循环程序的缺失次数又是多少？
## 注：一个缓存行可以放四个数组元素。
假定缓存的行数增多，缓存行的大小不变，而矩阵的行列等比例增大，那么版本1缓存缺失的次数会增多，因为对于矩阵的每一行也会发生多次缓存缺失(大约$\frac{矩阵元素个数}{缓存行大小}$次)；如果缓存的行数与矩阵的大小差不多，那么版本2的缓存缺失的次数可以减小至与版本1相当，否则如果缓存行数明显小于矩阵行数，则大约会发生$矩阵元素个数$次缓存缺失。

MAX = 8，缓存行数 = 4时，版本1会有16次缓存缺失，版本2会有64次缓存缺失。

## Q3 在表2.2中，虚拟地址由12位字节偏移量和20位的虚拟页号组成。如果一个程序运行的系统上拥有这样的页大小和虚拟地址空间，这个程序最多可以有多少页？
2 ^ 20 = 1048576个

## Q4 假设一个程序需要运行$10^{12}$条指令来解决一个特定问题，一个单处理器系统可以在$10^6$秒（大约11.6天）内完成。所以一个单处理器系统平均每秒运行$10^6$条指令。现在假设程序已经实现并行化，可以在分布式内存系统上运行。该并行程序使用p个处理器，每个处理器执行$10^{12}/p$条指令，且需要发送$10^9(p-1)$条消息。执行该程序时，不会有额外的开销，即每个处理器执行完所有指令并发送完所有消息之后，程序就结束了，而不会有诸如等待消息等时间造成的延迟。那么，
### a.假设发送一条消息需要耗时$10^{-9}$秒。如果并行程序使用1000个处理器来运行，每个处理器速度和单个处理器运行串行程序速度一样，那么该并行程序的运行需要多少时间？
$10^{12} / 10^6 / 1000 + 10^{-9}*10^9*999 = 1999s$

### b.假设发送一条消息需要耗费$10^{-3}$秒，这个使用1000个处理器的并行程序运行需要多少时间？
$10^{12} / 10^6 / 1000 + 10^{-3}*10^9*999 = 999001000s = 11562.5d$

## Q5 对各种分布式内存互连网络，写出其包含的链路总数的表达式。
## 注：只计算网络内部的链路数，即交换器之间的链路。
设节点个数为p

| 结构 | 链路总数 | bisection width |
| --- | ------- | --------------- |
| 环 | $p$ | $2$ |
| torodial mesh($p=q^2,q为偶数$) | $2p$ | $2\sqrt{p}(即2q)$ |
| 完全图 | $\frac{p(p-1)}{2}$ | $\frac{p}{2}$ |
| 超立方体($p=2^d$) | $\frac{p}{2}\log_2p$ | $\frac{p}{2}$ |

| 结构 | 交换机总数 | 链路总数 |
| --- | ------- | ------- |
| 交叉开关矩阵 | $p^2$ | $2p^2(若只考虑矩阵内部,则为2p(p-1))$ |
| omega网络($p=2^d$) | $\frac{p}{2}\log_2p$ | $p(\log_2(p)+1)(若只考虑omega网络内部,则为p(\log_2(p)-1))$ |

## Q6 为了定义间接网络的等分宽度，我们将处理器分为两组，每组拥有一半数量的处理器。然后，在网络的任意处移除链接，使两组之间不在连接。移除的最小连接数就是该网络的等分宽度。请说明一个8x8的交叉开关矩阵的等分宽度小于或等于8，并说明一个拥有8个处理器的omega网络的等分宽度小于等于4
1. 8x8的交叉开关矩阵
- 无论怎样划分，只需要切断每个处理器进入交换网络的总计8条链路，即可让任何两个处理器都不能通信
2. p=8的omega网络
- 从上到下，依次轮流把处理器分到第一组和第二组，只需切断第一组进入omega网络的总计4条链路，即可让第一组和第二组不能通信

## Q7
### a.假定一个共享内存系统使用监听缓存一致性协议和写回缓存。并且假设0号核缓存里有变量x，并执行赋值语句x=5。1号核的缓存里没有变量x。当0号核更新x后，1号核开始尝试执行y=x。y被赋的值是多少？为什么？
是x的原始值，因为0号核执行了`x = 5`后没有把它写会内存

### b.假定上面的共享内存系统使用的是基于目录的协议，则y的值将是多少？为什么？
结论和原因都同上

### c.你能否为前面两部分中发现的问题提出解决方案？
在硬件设计的层面：对于write-back的缓存，需要在后来的核发出读请求时在核间广播，监听到这个广播的核如果发现自己的cache内含有这个读的地址的已经修改过的cache，需要把它写回内存

如果硬件这样实现的开销太大，也可以由程序员来手动控制这一过程：通过设置某个标志来表示x已经成功更新，从而允许其它的核来读取它

## Q8 假定$T_{串行} = n$，并且$T_{并行} = n / p + \log_2p$。时间单位为毫秒。如果以倍率k增加p，那么为了保持效率值的恒定，需要如何增加n？请给出公式。如果我们将进程数从8加倍到16，则n的增加又是多少？该并行程序是可拓展的吗？该并行程序是弱可扩展的吗？
$$E = \frac{n / p}{n / p + \log_2p} = \frac{n' / kp}{n' / kp + \log_2kp}$$
$$solved\ to\ n' = n\frac{k\log_2kp}{\log_2p}$$

对于从p=8增加到p=16的例子，$\frac{n'}{n} = \frac{2\log_216}{\log_28} = \frac{8}{3}$

它是可拓展的，因为$n'$有解

它不是弱可拓展的，因为$\frac{n'}{n} > k$