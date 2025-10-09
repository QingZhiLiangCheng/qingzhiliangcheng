---
{"created":"2025-09-28T10:00","updated":"2025-10-07T18:21","dg-publish":true,"permalink":"/DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 04 Query Execution & Processing I/","dgPassFrontmatter":true,"noteIcon":""}
---

![[04-execution1.pdf]]

### Overview
前两节课聚焦的是数据存在的形式，我们会使用合适的列式压缩格式和编码方案确保能够最大限度地减少从磁盘或远程对象存储中提取数据并将其加载到内存中的数据量
并且我们希望理想情况下能够在编码形式或者压缩形式上直接进行处理
接下来的几节课将聚焦于如何执行查询操作
OLAP的世界里，一切也是顺序扫描
本课程的核心实际上实质上是探讨如何构建一个数据库系统，使其能够针对给定的一组查询，能够高效运行
![Pasted image 20250928101150.png|500](/img/user/accessory/Pasted%20image%2020250928101150.png)
所有这些方面都是至关重要的，可以叠加
这其中已经讨论过Data Encoding和Compression以及简单的讨论过Data Skipping
这实际上也是从工程角度理解，权衡

同样，我们希望高效利用硬件资源
安迪（Andy Pavlo）的非科学Top-3优化总结：  
→ 数据并行 (Data Parallelization)，例如：向量化 (Vectorization)  
→ 任务并行 (Task Parallelization)，例如：多线程 (Multi-threading)  
→ 代码专用化 (Code Specialization)，例如：预编译 / 即时编译 (JIT)


在高层次上，optimization goals分为一下三个方面
- Approach 1: Reduce Instruction Count: 
- Approach 2: Reduce Cycles per Instruction: Execute more CPU instructions in fewer cycles. 
- Approach 3: Parallelise Execution

Use fewer instructions to do the same amount of work. 编译器能帮上忙，比如gcc传递O2跟Og就不一样，但是老师提到了一个点是人们通常不会启用O3级别的编译优化来部署生产数据库，因为可能会导致一些复杂问题，可能会以一种不正确的方式重排事物
![Pasted image 20250928102439.png|400](/img/user/accessory/Pasted%20image%2020250928102439.png)

我们希望其操作所需的数据，处理所需的数据，能够位于L1, L2缓存中，甚至是CPU寄存器；换句话说，我们希望减少由内存读取和存储导致的缓存未命中，最大化数据在操作符和查询计划中的局部性，以便这些数据能够驻留在CPU缓存中 -- 但问题是我们人类在自然编写代码时，并不能总以CPU期望的最佳方式来执行指令，由于乱序超标量CPU，我们平时所写的代码实际上可能是与CPU不同的，所以我们需要审视在执行查询或构建系统时所采用的算法能够确切考虑到CPU预期和需求，并尝试以一种能够为我们生成所需结果的方式来设计代码，然后编译器帮我们完成这一任务

明确术语
![Pasted image 20250928104458.png|500](/img/user/accessory/Pasted%20image%2020250928104458.png)
query plan是DAG和operator(算子)这个在之前的CMU15445和之前的bustub中都很了解了
operator instance是一个唯一的数据分片上对一个算子的调用执行，算子实例是算子的执行体，如果将一个数据分成十个片，那么比如选择算子就会实例化10次
Task是由一个或多个算子实例组成的序列，我的理解其实就是纵向的流水线吧，可能一个片的整个过程叫任务？
图中由两种流水线，流水线实际上规定了我们能够处理一批元组的方式
当到达一个操作符后，我们需要查看流水线中所有的元组后，才能继续推到上层(但这里流水线中的元组并不多，分片了)

**today's agenda**
- MonetDB/X100 Analysis
- Processing Models
- Plan Processing Direction
- Filter Representaion
- Parallel Execution

### MontDB/X100 Analysis
这是我们MontDB X100论文所介绍的东西，论文是2005年的论文，主要是对内存工作负载下的瓶颈进行了低层次分析，探讨了OLAP查询所遇到的瓶颈，他们审视了当时所有的系统，并展示了OLAP大规模查询各个操作的耗时，并且发现当时现有系统并未针对当时现代的英特尔所提出的乱序超标量CPU架构进行优化，他们给出了如果重新设计数据库针对CPU所需的选择，即数据向量化的流动，指令等
#### CPU Overview
现代的CPU有两种重要的技术，一个是pipeline(流水线), 一个是superscalar, Out-of-Order Execution(超标量,乱序执行)，这两个技术在计算机组成原理的最后都提到过，在NJU jyy老师的操作系统课上也提到过超标量乱序执行可能所造成的并发问题。
不管是流水线还是超标量技术，都是建立在一种乐观，或者是预测的状态下，他很快，但是遇到Dpendencies(依赖关系)和Branch Prediction(分支预测)的情况，反而可能会造成一些问题
- 依赖关系: 当一条指令需要上一条指令的计算结果时，那就只能等
- 分支预测：当遇到类似if-else的循环的时候，CPU会预测一个分支进行执行，但如果一旦发现错误，会扔掉流水线中的所有预测后所执行的代码，重新执行

在分支预测这一方面，实际上英特尔就做的很好了，而且分支预测这个东西很复杂，它就像是一种秘方，是英特尔没有公开的东西
不管怎么说，paper中所提到的Dpendencies和Branch Prediction这两种情况，是想告诉我们，如果系统架构中包含大量条件判断，可能会适得其反
在数据库中，最频繁的分支情况出现在顺序扫描期间的过滤操作，这几乎是不太可能总是正确预测的，因为我们数据库自己都可能预测失败，更不用说CPU对数据库的信息一无所知了
C++20提供了一种新的特性[C++ attribute: likely, unlikely (since C++20) - cppreference.com](https://en.cppreference.com/w/cpp/language/attributes/likely)来给编译器指导，它制定了某个条件子句是可能的还是不太可能的，Clickhouse, Postgres会有这个功能，DuckDB没有；这并非是CPU的提示，我们无法直接告诉CPU我们需要沿着某条路走，这只是给编译器的一个提示，使其有可能重新组织代码，将可能的路径放在顶部
但是在Intel担任编译器工程师的大佬有一篇文章是说不要用这种方法hhh https://blog.aaronballman.com/2020/08/dont-use-the-likely-or-unlikely-attributes/


