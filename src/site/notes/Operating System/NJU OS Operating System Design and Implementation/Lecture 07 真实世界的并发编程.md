---
{"created":"2025-09-02T08:15:27.644+08:00","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 07 真实世界的并发编程/","dgPassFrontmatter":true,"noteIcon":""}
---

**today's agenda**
- 高性能计算中的并发控制
- 数据中心里的并发控制
- 我们身边的并发编程

#### 高性能计算中的并发控制
高性能计算 -- “A technology that harnesses the power of supercomputers or computer clusters to solve complex problems requiring massive computation.” (IBM)
实际上就是massive computation

以计算为中心
- 系统模拟：天气预报、能源、分子生物学
- 人工智能：神经网络训练
- 矿厂：纯粹的 hash 计算

这里老师提到了block chain, 老师的point是它确实做到了去中心化，但是浪费了很多很多的能量

高性能计算的主要挑战
- 计算任务如何分解
- 线程之间如何通信

首先 计算图需要容易并行化 这里老师举了一个动态规划的计算图的例子 其实这个图之前我在学labuladong的算法的那本书中见到过类似的
![Pasted image 20250902084757.png|500](/img/user/accessory/Pasted%20image%2020250902084757.png)
在动态规划中，下一步的结果需要依赖前面的结果，其实可以根据拓扑排序来分成step1, step2... 分别运行在CPU1, CPU2...
其实在这里我突然想起来昨天晚上在[[DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 03 paper-1 The FastLanes Compression Layout\|Lecture 03 paper-1 The FastLanes Compression Layout]]看到的fastlanes为了增加并行度转置了数据排列的顺序(因为一些压缩算法也是需要依赖前面的结果)，比如"04261537" 0,1 4,5这样算
但上面提到的事实上是多处理器编程，而高性能计算中超级计算机有多个机器，需要先分配到机器上再分配到机器上的不同线程
线程直接需要互相通信，有一些主流的编程框架，可以想象成是一些高级的线程库，老师提到了MPI和OpenMP

为什么高性能计算可以成立？ 事实上在超级计算机上运行的任务的并行度比我们想象的都要高
比如说模拟全中国的汽车的路线等等 在物理世界中物体，分子等都有一个很重要的性质--局部性，也就是说，前一秒这个汽车在南京，下一秒也会在这附近
所以对于一个物理系统 可以横着竖着切割 然后再处理边界就可以了
![Pasted image 20250902090431.png|300](/img/user/accessory/Pasted%20image%2020250902090431.png)
突然想起来我当时看Transform的论文的时候了解过切割这个

小栗子
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/mandelbrot.c

gcc -Og mandelbrot.c -o mandelbrot.out -lm
```
这实际上是在复平面上画了一个图形，多个 CPU核心来加速曼德博集合的计算过程。图像被分割成多个垂直的条带，每个线程负责计算其中一个条带的像素值
![mandelbrot1.gif|500](/img/user/accessory/mandelbrot1.gif)

![mandelbrot8.gif|500](/img/user/accessory/mandelbrot8.gif)
事实上是6400 * 6400的 还生成了一个ppm文件 可以转换成jpg文件
![Pasted image 20250902094203.png|500](/img/user/accessory/Pasted%20image%2020250902094203.png)

book -- - [Parallel and Distributed Computation: Numerical Methods](https://web.mit.edu/dimitrib/www/pdc.html) 

#### 数据中心里的并发编程
数据中心 -- “A network of computing and storage resources that enable the delivery of shared applications and data.” (CISCO)
前面的高性能计算着重于模拟和人工智能，数据中心着重于数据存储
这有点涉及到我最近看的CMU15721中的内容了

主要挑战
- Consistency 一致性
- Availability 时刻保持可用
- Parition tolerance 容忍机器离线

![Pasted image 20250902100849.png|500](/img/user/accessory/Pasted%20image%2020250902100849.png)
老师举的是刷微博的例子：首先是有好多数据中心在不同的地理位置，为了保持数据不会丢失，一般会保存好多个副本
consistency是说我在这边取关 希望能立马同步过去 然后你妈马上就看不见了
availability是希望没刷一下 立马就能加载出来
parition tolerance 就是容错 两个数据中心直接实际上是有延迟的
事实上这三点是不可能同时做到的
![Pasted image 20250902101250.png|500](/img/user/accessory/Pasted%20image%2020250902101250.png)
比如想要availability和consistency就要用单机的数据库 比如Oracle MySQL

那数据中心和并发编程有什么关系？
虽然分布式系统是操作系统中的一个重要分支，但是OS课是传统的操作系统，所以我们只关注某个数据中心中的某一台计算机，这台计算机一方面进行存储，一方面与其他计算机通过网络相连
如何用好一台计算机？如何使用一台计算机经可能多地服务并行的请求？
我们有的工具
- 线程threads
- 协程coroutines

对于threads来说，就像上面mandelbrot看到的，可以创建好多个线程，这些线程就在操作系统里，操作系统可以把线程放到不同的CPU上去执行，也可以好多线程放在同一个CPU上执行
但是在同一个CPU上进行线程切换是需要保存很多上下文的，这是一个从用户模式到内核模式的特权级别转换，操作系统对线程中的内部逻辑代码一无所知，不知道哪些寄存器是重要的，哪些是临时的，操作系统会保存所有的寄存器，包括rax, rbx, rcx... 还有一些系统寄存器rip, rsp, 内核执行调度算法选择下一个要运行的线程然后切回用户模式
而协程是发生在同一个线程内部的用户空间，无需陷入内核，会保存很少的寄存器(通过编译器分析)
![Pasted image 20250902105917.png|400](/img/user/accessory/Pasted%20image%2020250902105917.png)
如果说线程有好处的话，其实就是可以多处理器，但会每个线程都会占用可观的操作系统资源
但协程不受操作系统调用 一旦碰到read这种阻塞式的系统调用，它实际上是让它所在的线程T1去执行这个系统调用。当内核发现这个操作无法立即完成时，它会把整个线程T1挂起（放入等待队列），然后让CPU去干别的事 -- 整个线程上的协程都被冻结了
怎么解决 -- go语言 -- Goroutine
![Pasted image 20250902110939.png|500](/img/user/accessory/Pasted%20image%2020250902110939.png)
go的核心是偷偷将read的block的系统调用换成unblock的系统调用，等待的时候先让其他协程运行，当检测到数据到达的时候，再回来

go的想法是 Do not communicate by sharing memory; instead, share memory by communicating.
不要用共享内存做系统通信 不去用条件变量 -- 之前学的是底层原理 只是教会我们 条件变量可以解决基本所有的并发问题
如果生产者-消费者能解决绝大部分问题，可以直接提供一个api

#### 我们身边的并发编程

Web2.0 Users were encouraged to provide content, rather than just viewing it.”
什么成就了今天的Web2.0?  -- 浏览器的并发编程 -- Ajax(Asynchronous JavaScript + XML)
Ajax的过程就不说了 -- 反正写过
总之浏览器就是一个DOM Tree，我们可以通过开发者工具手动改变里面的节点的内容，也就意味着我们可以用代码 JavaScript来改变节点的内容或者删除增加一个节点
越来越多的人 都参与到 网页的制作中 -> 生活会变好
我们希望的是越来越多不懂代码的人可以参与到网页的制作来，他们不懂生产者消费者，不懂并发 -> 所以这里的一个挑战就是只有一个线程，每一个基本执行单位都是一个事件，这个事件一旦开始执行就会执行到结束，这样就避免了并发bug
每一个事件计算的事件是很短的，唯一成问题的是网络请求，网络请求的时间不可预估，所以不可能把网络请求放到一个事件中完成，当网络请求出去的时候这个事件就结束了，请求结束后会再创建一个新的事件
![Pasted image 20250902203011.png|300](/img/user/accessory/Pasted%20image%2020250902203011.png)
所以是单线程 没有锁 这叫异步编程
- 好处是并发简单了很多，事件不并行，API(耗时间的操作)还是并行的 -> 这个设计减少了并发bug
- 坏处是大量shi山 callback hell

导致 callback hell 的本质：人类脑袋里想的是 “流程图”，看到的是 “回调”
有更好的api叫 promise -- 用语言的形式 来描述 流程图
