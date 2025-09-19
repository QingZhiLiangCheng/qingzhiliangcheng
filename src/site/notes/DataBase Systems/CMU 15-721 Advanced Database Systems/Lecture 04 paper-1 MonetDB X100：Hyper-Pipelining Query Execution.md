---
{"created":"2025-09-14T09:45","updated":"2025-09-14T09:47","dg-publish":true,"permalink":"/DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 04 paper-1 MonetDB X100：Hyper-Pipelining Query Execution/","dgPassFrontmatter":true,"noteIcon":""}
---


![[Lecture04paper MonetDB X100 Hyper-Pipelining Query Execution.pdf]]

这应该算是向量化执行的提出论文吧，这篇论文实际上可以分为三个部分
- 第一部分分析了现代CPU的超标量(超流水线)技术，也正是因为超标量技术，以及内存带宽瓶颈，导致了原来的火山模型和旧版MonetDB的全列物化并不适用，所以paper后来提出了向量化执行的引擎
- 第二部分是在不同的数据库上测试TCP-H的Query1来研究问题的根源，这里测试了DBMS "X", MySQL这些传统行数据库，Oracle, SQL Server, DB2这样的商业数据库(我记得这些数据库也大多数是行数据库)，以及旧版的MonetDB(列式数据库)，发现了时间大部分并不是集中在计算的操作上，所以后面提出了向量化执行的引擎X100
- 第三部分是X100引擎的工作原理

### 超标量技术
我第一次接触超标量技术应该是在计算机组成原理的课上，第二次是在CSAPP上，甚至我记得好像下面的这张图我也在CSAPP上见过
![Pasted image 20250918185923.png|400](/img/user/accessory/Pasted%20image%2020250918185923.png)
这张图已经很老了，因为这篇论文是2005年的，但总体上是说，CPU频率的增长速度比单纯的只是晶体管尺寸减半数量翻倍还要快，这是因为流水线技术的实现
但是其实CSAPP上也提到了，流水线也存在一定的问题
- 数据依赖: 连续的数据如果存在依赖，很难并行
- 分支预测：如果预测失败，就要推翻流水线重来

超流水线的出现使得编译器优化，循环流水线，分支预测的能力变得很重要
当然，这篇论文和CSAPP当时也提到了，可能通过改变代码来消除if分支，但是paper中提到了，通过改变代码来消除if的形式效率并不高
![Pasted image 20250918190748.png|400](/img/user/accessory/Pasted%20image%2020250918190748.png)

这是paper中做的测试，左边是对同样的代码 if分支和不使用if分支的版本，右边是测试结果，横轴是选择率，纵轴是时间
显然在选择率为50%的时候，分支预测最难做，导致的性能最差，当然如果使用消除if的版本，性能会好一点
但有趣的是，在Itanium2上，即便是普通的分支版本也同样高效且不受选择率影响，因为它的编译器会自动将分支转换成硬件支持的预测执行代码。
我的理解是，实验说明，要提高计算机的性能是要改成硬件适合的形式传入，所以后面是通过改变数据库的架构改变了数据传入CPU的形式

### TPC-H Query 1
#### Overview
首先其实这也不是第一次见到TPC-H这个测试了，我在[[DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats\|Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats]]中其实看到过这个测试的名字，但是最后Andy老师他们在写这篇论文的时候并没有用这个测试，是因为Andy老师的这篇paper中是论证了真实世界的数据的偏移，所以他们用了符合真实数据的自己生成的测试集，但这篇论文是05年的论文了，所以用TPC-H也很正常hh
**什么是TPC-H？**
简单来说，TPC-H 就是一套模拟现实世界中 商业智能（Business Intelligence, BI） 和 数据仓库（Data Warehouse） 应用的数据集，它提供了数据模型，查询，衡量性能的指标
**为什么选取了Query1？**
![Pasted image 20250918192528.png|400](/img/user/accessory/Pasted%20image%2020250918192528.png)
这是TPC-H的Query 1，这个查询是CPU密集型的，TPC-H基准测试在一个1GB的数据仓库上运行，其大小可以通过一个扩展因子（Scaling Factor, SF）来增加。Query 1是对`lineitem`表（包含SF * 600万行数据）的一次扫描，它会选中几乎所有的元组（SF * 590万行），并计算了列与常量的加减乘以及八个聚合操作
所以选择Query1的本质是因为想要研究的是计算效率，而不是查询优化，所以选择这个query1中其实没有链表操作，而且过滤操作简单，where子句事实上会选中98%的数据，这意味着实际上分支预测基本上100%会正确，排除了之前所说的分支预测失败的干扰

**测试结果**
![Pasted image 20250918193208.png|350](/img/user/accessory/Pasted%20image%2020250918193208.png)
其实根据测试结构能把表格中的系统分为四个梯队

| 梯队              | 数据库                      |                    |
| --------------- | ------------------------ | ------------------ |
| 第四梯队 - 传统行式数据库  | MySQL, DBMS "X"          | 最慢                 |
| 第三梯队 - 商业数据库    | Oracle, SQL Server, DB2  | 比传统数据库行式数据库好，但还是较慢 |
| 第二梯队-旧版本列数数据库   | MonetDB/MIL              | 比传统数据库快了将近一个数量级    |
| 第一梯队 - 理论极限与新引擎 | hand-coded, MonetDB/X100 | 快                  |



####  On MySQL
![Pasted image 20250918202634.png|400](/img/user/accessory/Pasted%20image%2020250918202634.png)
这是MySQL 4.1执行TPC-H Query 1的`gprof`性能剖析跟踪
- `cum.` (Cumulative / 累积百分比): 从上到下，每一行表示到当前函数为止，总共占用了多少百分比的CPU时间。
- `excl.` (Exclusive / 独占百分比): 这是最重要的列。它表示当前这个函数本身（不包括它调用的其他函数）占用了多少CPU时间。
- `calls` (调用次数): 这个函数在整个查询执行过程中被调用了多少次。
- `ins.` (Instructions / 指令数): 平均每次调用这个函数，会执行多少条机器指令。
- `IPC` (Instructions Per Cycle / 每周期指令数): 这个函数执行期间的CPU效率。理想值远大于1，而这里普遍低于1。
- `function` (函数名): 被分析的MySQL内部函数。

真正的数学计算， `Item_func_mul::val` (乘法): 1.3%，`Item_func_minus::val` (减法): 1.0%， `Item_func_plus::val` (加法): 0.5%，聚合函数如 `Item_sum_sum::update_field` (3.1%) 和 `Item_sum_avg::update_field` (2.9%)，只占了不到10%，而大部分时间都用在了`ut_fold_ulint_pair` (11.9%), `ut_fold_binary` (8.5%), `memcpy` (5.8%)，这是在做与哈希表，内存拷贝等操作，更多的`rec_get_bit_field_1` (2.6%), `rec_get_nth_field` (2.4%), `rec_get_1byte_offs_flag` (1.0%), `rec_1_get_field_start_offs` (1.0%): 这一系列`rec_get_*`函数，它们的作用就是在MySQL的内部记录（元组）格式中定位并提取某个字段的值(MySQL是行存储，而这种TPC-H的这种是针对列的操作，所以都得从行中找出来)
另外，`Item_func_plus::val`每次加法操作的成本高达38条指令()，但事实上在硬件层面，一个加法指令是这样的
```ash
LOAD src1, reg1
LOAD src2, reg2
ADD reg1, reg2, reg3
STOR dst, reg3
```
这个性能跟踪是在一台配备MIPS R12000 CPU的SGI机器上进行的，该CPU每个周期可以执行三条整数或浮点指令和一次加载/存储，平均操作延迟约为5个周期
这段代码的限制因素是三个加载/存储指令，因此MIPS处理器每3个周期可以完成一次 `*(double, double)` 操作。这与MySQL的成本`#ins / IPC = 38 / 0.8 = 49`个周期形成鲜明对比
paper中给的解释是因为MySQL的调用并不是流水线，而是每次调用只计算一次加法
因此，一次加法由四条相互依赖的指令组成，它们必须互相等待。考虑到平均指令延迟为5个周期，这就解释了大约20个周期的成本。其余的则花费在跳转到例程、压栈和出栈上