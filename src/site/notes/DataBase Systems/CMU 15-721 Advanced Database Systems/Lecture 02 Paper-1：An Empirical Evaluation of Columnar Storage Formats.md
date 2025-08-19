---
{"tags":["CMU15721"],"dg-publish":true,"permalink":"/DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats (X. Zeng, et al., VLDB 2023)/","dgPassFrontmatter":true,"noteIcon":"","created":"2025-07-12T11:54:58.043+08:00","updated":"2025-07-31T11:15:03.762+08:00"}
---


![[Lecture-02-Paper-1：An Empirical Evaluation of Columnar Storage Formats (X. Zeng, et al., VLDB 2023).pdf]]

这是2023年的一篇论文，这篇论文是Andy老师写的唉 Andy老师的全称叫Andrew Pavlo，这是他在学术和正式出版物中的署名，Andy是Andrew的常见昵称，在教学、演讲、网站、社交媒体等更轻松的场合，他经常用的是 Andy Pavlo.
### Abstract
这篇论文主要对Rarquet和ORC两个主流开源列式存储格式进行了实验分析
Rarquet和ORC最初都是为Hadoop设计的，我查了一下Hadoop,是2006年诞生的用于大数据存储和处理的开源框架，主要用于分布式海量数据的存储和并行计算 而论文中提到的这个列存储格式是大约2010年代初为Hadoop生态设计的
但是从那时到现在 无论是硬件(CPU/SSD/GPU)还是工作负载(机器学习/实时查询)都发生了巨大变化，旧格式无法充分利用这些新特性。
这篇论文重新审视了Parquet和ORC这两个开源列式格式，深入内部结构，设计了一套基准测试来评估这两种格式在不同负载下的性能和存储效率，发现了一些优点，即有利于现代硬件和现实世界数据分布的方面设计选择
- 默认使用字典编码
- 在整数算法中优先考虑解码速度而不是压缩率
- 块压缩作为可选
- 嵌入更细粒度的辅助数据结构

同时，在实验中也发现了在GUP和机器学习场景下的设计问题
为此，作者提出了一些未来格式设计的建议，以适应现代技术趋势

### Introduce
在之前已经出现了列式存储并且广泛应用于数据分析领域 因为
- Irrelevant attribute skipping(跳过无关属性)
- Efficient data compression(高效压缩)
- Vectorized query processing(向量化查询处理)

在2010s，当时很多公司每天都会产生PB级别的数据，需要高效的处理引擎，各类组织开始开源大数据处理引擎，在paper中提到的有
- Hive: 最早将SQL转换为Hadoop MapReduce作业，适合批处理
- Impala: Cloudera 推出，支持实时交互式查询，比 Hive 快得多
- Spark：内存计算引擎，支持批处理、流处理、机器学习等场景。
- Presto（Trino）：Facebook 开发的分布式 SQL 引擎，可跨多种存储系统执行查询（如 Hive、Kafka、S3）。

为了让不同基于Hadoop的查询引擎支架能够共享数据，有提出了开放的列式存储格式，如Parquet和ORC，现在这两个格式已经成为data lake和data warehouse的真实标准
但是Rarquet和ORC都是2013年的格式，已经过去了10年了，在这十年以来，硬件和层面都发生了巨大的变化
- 持久存储器的性能提高了几个数量级
- data lake兴起，云存储兴起，在云存储中高宽带，高延迟
- 软件方面学术界提出了很多新的轻量级的压缩方案以及索引和过滤技术

除此之外，作者发现已有的存储格式的研究主要集中在
- 测量基于Hadoop的查询引擎的端到端的性能，所谓的端到端，是从发出SQL查询，到查询引擎调度，读取数据，数据处理，最终返回结果这一整个流程的耗时，作者提出这种方法混合了多个因素，比如引擎优化的差异，网络延迟等等；
- 这些研究没有对列存储格式的设计决策进行深入的分析和权衡；
- 使用了不符合真实世界的数据集，在之前的研究中，大多数使用的都是人工合成的工作负载，没有考虑到真实世界的数据倾斜，所谓数据倾斜就是世界上产生的数据不是均匀分布的，而是出现倾斜的，比如说有90%的商品属于食品类的商品

所以作者提出这篇paper的主要任务是: 分析常见的列存储文件格式，保留优点，改进缺点，并为下一代存储设计提供思路
- 使用真实的数据集
- 对Parquet和ORC的主要组成部分进行了全面的分析
	- Encoding(编码方式)
	- Block Compression(块压缩)
	- Metadata Organization(元数据组织)
	- Indexing and Filtering(索引与过滤)
	- Nested Data Modeling(嵌套数据建模)
- 如何高效地支持常见的机器学习工作负载  --  对GPU是否友好

研究结果发现
- Finding 1:
	- Parquet的文件大小占优势：原因是its aggressive dictionary encoding 其实看了后面就知道了Parquet先对数值类型做了Dictionary Encoding但ORC没有，并且Parquet的Dictionary Encoding更激进？
	- Parquet的列解码更快 -- 因为Parquet具有更简单的整数编码算法
	- ORC在selection pruning(选择性修剪)方面更有效 -- 因为ORC的zone map具有更细的粒度 -- zone map是什么？ -- 一种索引结构，记录每一块数据的某一列的最大值和最小值，比如谓词搜索范围是x>500，而某个zone的最大值是300，就可以直接跳过这个区域
- Finding 2: 现实世界的数据集中，大多数列具有较小的唯一值 -- 换句话说其实就好比有好多条数据，但是选项就这个几个，比如就几十个省份，就这么多科目 -- 这是非常适合使用字典压缩的 -- 就好比男用1 女用0差不多 -- 所以说压缩，字典编码 的算法的效率 就是关键
- Finding 3: 使用更快的解码方案来降低计算成本与追求更积极的压缩来节省IO更好 -- 一定要用压缩，但是要做好一个取舍 要好解码 这比压缩率更高更重要  -- 这其实也是 现在计算比存储的一个思想 在Finding4中其实也提到了
- Finding 4: Parqet和ORC的辅助结构太简陋，比如zone Map和Bloom Filter等…… 其实这在CMU15445中都提到过，不管是Zone Map还是Bloom Filter -- 这其实也是空间换时间的一个思想，again，现在计算比存储更看中
- Finding 5: 当前格式对机器学习负载支持不好 -- 常见的ML负载其实包括
	- 训练阶段的高维特征访问 -- 效果不佳
	- 向量检索中的Top-k相似性搜索(向量点积或余弦相似度) -- 效率低
	- GPU加速训练 -- 当前格式并行单元太少 无法充分利用GPU


### Background and Related Work
2010年初，随着大数据生态兴起，出现了一批开源文件格式
最早出现的是Apache Hadoop的两款row-oriented formats, SequenceFile和Avro. SequenceFile是以键值对的形式组织数据；Avro是一种基于Json schema的数据格式
但同时出现了好多column-oriented的数据库系统，比如C-Store(MIT的列存储数据库原型), MonetDB, VectorWise... 
2011年，Facebook/Meta发布了首个开源列式存储格式 -- RCFile，是一种列式存储和行式存储的折中 -- 先按行分区 每个分区再按列存储
2013年，Meta在RCFile的基础上做了改进，参考了PAX模型，推出了ORC文件格式 -- PAX是一种混合存储模式；同年，Twitter和Cloudera联合发布了Parquet. Parquet参考了PAX模型和Dremel的record-shredding and assembly算法，Dremel是Google提出的一种超大规模分析平台，可以支持嵌套字段（比如嵌套Json）压缩展开
之后，Parquet和ORC都被列入Apache基金会顶级项目，变成了当时主流的标准。目前主流的数据处理平台入Hive, Presto, Trino, Spark都是基于Parquet和ORC的，甚至有自己格式的数据库如Google BigQuery, Amazon RedShift, Snowflake也都支持
其实华为也有一种列存储格式叫CarbonData，但是与Spark集成的太过密集，很多研究在测试的时候都没把他作为一种通用的文件格式来测试，因为他比较受限于Spark；但是新的研究发现确实是不如Parquet和ORC.
另外，随着发展，各个科技公司开发了专用于自己的列存储格式，其实这是发展的必然趋势，这种趋势在计算机体系结构课程[[Computer Architecture/ETH Zurich 苏黎世联邦理工学院 Digital Design and Computer Architecture/lecture 01 Introduction and Basics\|lecture 01 Introduction and Basics]]中也提到过，Tesla为自己的自动驾驶设计了专门的芯片，指令集，系统等等，为的就是完全高效利用
- Capactior是Google内部使用的一种列式存储格式，用于BigQuery和Napa等多个系统
- Youtube针对其评论，视频源数据等结构复杂的数据设计了Artus，用于Youtube的Procella分布式数据库
- Alpha是Meta推出的格式 -- 专为机器学习训练设计

Arrow是一种列式的内存格式，他的主要思想是在内存中进行列式存储，不需要序列化或者反序列化，对于Parquet和ORC来说他们更像是压紧了，而且这个压缩是按块压缩的，所以拿到内存中是解压缩然后哪里面的数据，而对于Arrow来说，本来就在内存中按列存储好了的，更像是一种传输格式，并不是长期磁盘存储设计的格式，所以这篇paper并没有评估Arrow
然后文中提到了最近的Lakehouse趋势，只是加了层 不改变存储格式，lakehouse基本的架构已经很熟悉了 详见[[DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 01 paper-1： Lakehouse： A New Generation of Open Platforms that Unify Data Warehousing and Advanced Analytics(M.Armbrust, et al., CIDR 2021)\|Lecture 01 paper-1： Lakehouse： A New Generation of Open Platforms that Unify Data Warehousing and Advanced Analytics(M.Armbrust, et al., CIDR 2021)]]
在高性能计算HPC中，还有一类专门的科学数据存储格式，比如HDF5, BP5, NetCDF, Zarr等广泛应用于科学模拟，气候建模，基因组等领域，主要是针对复杂数据，可能是多层次组织之类的数据，通常是多维数组存储，而不是按列示存储，不支持列示存储特性，所以数据库很少使用

### Feature Taxonomy
在这一节，会介绍Parquet和ORC的一些特征，对于每个特征，都会介绍Parquet和ORC的共同设计，然后介绍直接的差异
![Pasted image 20250728132943.png](/img/user/accessory/Pasted%20image%2020250728132943.png)
但是值得注意的一个点是，我们论文中的说法可能会跟Parquet和ORC的一些说法不同，但本质上是一样的
![Pasted image 20250728133525.png](/img/user/accessory/Pasted%20image%2020250728133525.png)
比如对于论文中Row Group，在Parquet和ORC中的叫法不同，但本质上都是一种将tuple先按行分配再按列分配，而那个按行分配 就是分成了 行组 Row Group
包括对于Zone Map，Parquet和ORC中的叫法和实现方法也是有差别的，但本质上最后的效果是一个东西

> [!tip] Feeling
> 另外，这里我多说一嘴，在3.1的时候，其实是提到了PAX格式，vectorized query processing(向量化查询处理) ，包括一直在提的zone map，现在看的时候都会记起来在CMU15445中都学过，啊啊啊啊啊啊啊 就很奇妙的感觉  我以为我把学了的东西都还给Andy老师了 但是碰到的时候我却知道
> 

Parquet和ORC的示意图
![Pasted image 20250728134652.png](/img/user/accessory/Pasted%20image%2020250728134652.png)

#### Format Layout
首先，Parquet和ORC都使用的PAX(Partition Attributes Across)格式, 这是一种混合存储格式，结合了行存储和列存储的优点，其实这在CMU15445的基础课程中学过[[DataBase Systems/CMU 15-445：Database Systems/Lecture 05 Storage Models & Compression#storage models\|Lecture 05 Storage Models & Compression#storage models]]，当时学的时候是介绍到了行存储(N-array Storage Model(NSM), row storage), 纯列存储(Decomposition Storage Model(DSM), column storage), 以及Hybrid Storage Model: Partition Attributes Across(PAX)，而且当时Andy老实说过说现在所说的列存储都是指的第三种PAX，而且还举到了Parquet和ORC的例子，只不过当时我不知道这是啥
总之，这里的核心思想是行存储和列存储的一种结合，表首先被划分为多个行组(row group), 每组包含多各行，然后在row group内部按列划分，形成了列块(column chunk).
这样的结构的优点在于不会向行存储那样引入额外的无用数据，因为有meta-data会标记每个列数据的偏移量，也不会像纯列存储那样数据不相邻。
而且PAX格式很适合vectorized query processing(向量化查询处理)，所谓的vectorized query processing, 其实就是数据在语法树中的传输是成批成批流动的，这种方式在[[DataBase Systems/CMU 15-445：Database Systems/Lecture 12 Query Execution Part 1\|Lecture 12 Query Execution Part 1]]中提到过，当时提到了Iterator Model, Materialization Models以及Vectorization Model. Iteractor Model的核心思想是一行一行的流，Materialization Models的思想是一整个表扔进去，而Vectorization Model是行组行组的那样
对于Parquet和ORC来说，都会对每个块先做lightweight encoding(轻量级编码), 在使用general-purpose block compression algorithms(通用块压缩算法)进行处理. 对于Encoding的处理，其实在[[DataBase Systems/CMU 15-445：Database Systems/Lecture 05 Storage Models & Compression#column-level\|Lecture 05 Storage Models & Compression#column-level]]中提到过一些方法，比如run-length encoding, bit-packing encoding, bitmap encoding, delta encoding, incremental encoding, dictionary encoding... 论文中前面是一直在提dictionary encoding. 在做完encoding之后，会进行compression, 以减少磁盘使用
Parquet和ORC的文件入口都是footer，这里说的是整个文件好多个Row Group外的footer, footer中会存很多meta-data(元数据)，包括表模式(table schema)(其实这个我们在CMU15445的Project中体会过), tuple count, row group meta-data, 每个column chunk的偏移量，以及zone maps for each column chunk等等，能看到图中Parquet和ORC的结构是不太一样的。
按图上的说法，对于Parquet是所有的Row Group外有一个footer，footer里面存了整个文件的metadata(比如table schema，row group的offset)同时在这里面还存了每个Row Group的metadata，然后每个Row Group中存放这每个column chunk的metadata，比如说offset, zone maps等等，这里的核心关键在于它是存在一起了
![Pasted image 20250728152257.png|500](/img/user/accessory/Pasted%20image%2020250728152257.png)
而ORC是有总的footer，但是每个column chunk的metadata是存在了对应的row group中
![Pasted image 20250728152534.png|500](/img/user/accessory/Pasted%20image%2020250728152534.png)

zone maps在这里已经不算是第一次提到了，zone map在[[DataBase Systems/CMU 15-445：Database Systems/Lecture 12 Query Execution Part 1\|Lecture 12 Query Execution Part 1]]中提到过
![Pasted image 20250327133943.png|500](/img/user/accessory/Pasted%20image%2020250327133943.png)
本质上就是会整合一些信息，来达到可以跳过这个column chunk的效果
Difference 1: Row Group大小的定义不同 -- Parquet按行数来定义row group的大小，比如论文中提到的例子是1M行一个row group；而ORC是使用固定的物理存储大小来定义row group，比如64MB作为一个row group -- Parquet的想法是保证行数进而保证vectorized query processing,但是如果一个表的属性特别多的话，这个row group的内存占用的会很大；而ORC的想法是保证内存的使用，但可能会因为表的属性多而导致条目不足
Difference 2: 压缩单元与zone mao的映射不同 -- Parquet是压缩单元和zone map单位是对应的，但ORC的压缩单元和zone map是独立设计  -- ORC能更灵活的单独控制压缩效果，但是可能值会跨边界

#### Encoding
首先lightweight compression是为了减少磁盘占用和降低网络传输成本
Parquet和ORC都支持Dictionary Encoding, Run-Lengh Encoding, Bitpacking
默认情况下，Parquet对每一列都积极的应用Dictionary Encoding,而不关注数据类型，而ORC只对字符串应用
Parquet对整数进行Dictionary Encoding的好处是可以将大值压缩，比如一个整数类型好多位100000000这样，且出现次数很多，通过Dictionary Encoding就会变成一个小的数；但是造成的结果是，可能会影响到Delta Encoding和Frame-of-Reference Encoding(POR)的第二次压缩
这里提到了Delta Encoding和POR, Dleta Encoding在[[DataBase Systems/CMU 15-445：Database Systems/Lecture 05 Storage Models & Compression#delta encoding\|Lecture 05 Storage Models & Compression#delta encoding]]中提到了，如下图
![Pasted image 20250206153946.png](/img/user/accessory/Pasted%20image%2020250206153946.png)
Dleta Encoding本质上是一种数据关系的差值，是一种相邻元素的差值，而POR是一种对于基准值（基准值是选出来的，对于异常特殊的值，比如说很大的值会单独存储）的差值，但本质上都是一种数据关系，而通过Dictionary Encoding可能会损失这种数据关系原来是连续递增 `[100, 101, 102, 103]`，编码后变成 `[2, 0, 3, 1]`，就没法通过Delta Encoidng和POR进行压缩了，所以说Parquet只能通过Bitpacking和RLE来进一步压缩Dictionary Encoding后的数据
Parquet和ORC对Dictionary Encoding存在着不同的策略，因为字典的建立需要占据一定的空间，所以Parquet和ORC都设置了一定的临界点。对于Parquet来说，他的核心是在于限制字典的总大小不能超过1MB（默认）如果NDV(number of different value)高，说明数据分散，如果建出来的字典大小超过1MB就会放弃；而ORC的策略是通过判断NDV Ratio，所谓的NDV Ratio是NDV与总行数的比值，如果不同的值占比太高，比如ORC的默认阈值可能是0.8，那就不适用字典压缩
刚刚上面提到了，Parquet由于对整数也采用了Dictionary Encoding，所以二次压缩只能采用RLE和Bitpacking，那选择哪一种呢？ Parquet采取的方式是比较重复次数是否超过8次，如果超过8次，就是用RLE，但是值得注意的是，作者在论文中说他们发现这个8是个硬编码，也就意味着并不可调，说明这可能是他们找到的最优的数值了
而ORC不同于Parquet，他没有对整数采用Dictionary Encoding, 所以他对整数的编码存在着四种方式，RLE, Bitpacking, Delta Encoding, POR. 在ORC中是存在一种规则驱动的贪心算法来决定在不同子序列中的最优压缩方式，规则是这样的
从序列的开始，使用一个最大值为512的look-ahead buffer(前瞻缓冲区)用来判断这段子序列使用何种方式
- 如果子序列中存在3-10个重复值，使用RLE
- 如果连续相同的值超过10个，或者值是单调递增递减，就是用Delta Encoding
- 对于即不重复也不单调的，如果没有异常值就用bitpacking
- 其他的使用PFOR或者其变体

总结一下就是ORC的编码方式多样，且效果可能会更好，但是解码慢，因为是多种编码混合使用的，需要先去找元数据查看这一段子序列是何种编码方式

#### Compression
Parquet和ORC都支持block compression(块级压缩)，通用的块压缩算法的特点在于类型不可知，所以说直接就把数据当纯字节流压缩了
大多数块压缩算法包含配置“压缩级别”的参数，以在压缩比和压缩/解压缩速度之间进行权衡，Parquet是把这个调整旋钮暴露给用户了可以选择不同的粒度进行压缩，而ORC只有两个选项，一个是optimize for speed(快但是压缩率低)，optimize for compression(压得小但慢)
有一个很有意思的事情是作者最后发现在现代硬件下，列式存储使用block compression对查询性能的帮助不大，甚至可能变差。因为压缩的只是原始字节，而不是列内部结构，对于本来通过encoding就压缩的很好的数据，解压，解码就变成了两次工作，在后文作者证明了这个结论

#### Type System
Parquet只支持以小组primitive types，如INT32, FLOAT, BYTE_ARRAY, 其他类型比如INT8, DATE, TIMESTAMP都是映射到原始类型上实现的，比如INT8实际上可能就存储为INT32，这样的type system比较简单，统一，紧凑，而且可以统一在INT32上做一些很好的压缩
而ORC的做法是INT8, INT16, TIMESTAMP, BOOLEAN等都有自己专属的reader, writer, encoder, decoder全套的实现，有点是可以对特定的类型进行优化，但是这样实现比较臃肿，系统更复杂
两者在complex types都支持struct(比如Json), List, Map. 但是Parquet不支持Union但是ORC支持. Union允许同一列具有不同的类型，在动态schema或者数据不一致的环境下，可能有优点，比如我们的Json日志中其中有一个是event_value，在Parquet下可能一开始只是浮点数，但是后期可能还要保存或者想变成字符串，那么Parquet就需要手动更改schema，而ORC支持`Union<TypeA, TypeB>`
并且有研究表明，如果如果 Parquet 支持 `Union`，可以更好地优化其内部的 Dremel 模型(一种嵌套数据表示和解析方式)

#### Index and Filter
Parquet和ORC都支持zone map和Bloom Filters. Zone map在上面已经提到过了，Bloom Filters在CMU15445[[DataBase Systems/CMU 15-445：Database Systems/Lecture 07 Hash Tables#chained hashing\|Lecture 07 Hash Tables#chained hashing]]有过介绍
在Parquet和ORC中的zone map都会记录某个区域内的最大值, 最小值, 行数等元信息，如果某个zone的值范围与查询条件不匹配，整个zone就可以被跳过
但是Parquet和ORC对于zone map的粒度有区别，Parquet和ORC都支持文件级和row group上使用zone map, 但是Parquet的最小粒度是page(可选) ，但ORC的zone map粒度是按行数配置的，默认是10000行一个zone，所以这就造成了前面多的问题，由于ORC的zone map和压缩单元可能不匹配，所以就会造成一些特殊性
过去旧版本Parquet的zone map最低粒度是放在每个page的page header中的，当指向检查zone map是否跳过的时候，就会出现很多随机I/O，因为我们是以Row Group为单位进行跳过的，在检查某一列的值是否匹配以确定是否跳过的时候，就需要把column chunk中的每个page都拿出来看一下page header, 换句话说，因为page中还有页体，所以存储是不连续的
在Parquet2.9.0版本后，这以问题通过新增的组件PageIndex得到了解决；但是这是可选的，如果不启用最低粒度，那么zone maps实际上是存储在图中游侠佳偶的column中的metadata中
![Pasted image 20250729142041.png|500](/img/user/accessory/Pasted%20image%2020250729142041.png)
而ORC的zone map一直存放在了Row Group的开头，所以一直都是连续的
![Pasted image 20250729142428.png|500](/img/user/accessory/Pasted%20image%2020250729142428.png)

Parquet和ORC的Bloom Filters都是可选功能。ORC中的Bloom Filter的最小粒度和zone map是相同的，并且存储在一起了，放在了Stripe(Row Group在ORC中的名字)开头的index区域；Parquet中的Bloom Filter是按column chunk粒度生成的，原因是PageIndex可选的，PageIndex的最低粒度是开到page的，所以事实上Bloom Filter是粒度更粗了的，但Parquet使用了一种叫做Split Block Bloom Filter(SBBF)的结构，可以提供更好的CPU缓存命中率和SIMD加速支持
Apache Arrow和DuckDB在读写Parquet时，只启用了row group粒度的zone map，InfluxDB和Spark启用了PageIndex和Bloom Filters； 这里可能的想法（我认为） 可能DuckDB和Apache Arrow认为最小粒度的zone map剪枝能力有限，但节省空间；而InfluxDB和Spark则是牺牲存储空间来换取更强的选择性过滤功能
读取ORC的时候，Arrow, Spark, Presto 都默认开启row index(zone map), 关闭Bloom Filter.
Zone map的剪枝效果是依赖于数据大致排序，同类值集中的，如果数据是完全乱序的，min/max 范围会很大，zone map 就基本剪不掉任何内容，而且一个有趣的问题是，随着硬件越来越快，IO成本的下降，或许就不需要更多的加入这些冗余的数据结构来减少IO了
#### Nested Data Model
![Pasted image 20250729145005.png](/img/user/accessory/Pasted%20image%2020250729145005.png)
随着半结构化数据集变得流行，开放格式都支持嵌套数据。  这里的半结构化数据其实在CMU15721的[[DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 01 lecture：Modern Analytical Database Systems\|Lecture 01 lecture：Modern Analytical Database Systems]]中提到了，非结构化数据是Lakehouse中提到的像PDF，音频，视频等，半结构化数据指的是JSON等
Parquet中的嵌套数据模型基于的是Google的Dremel，Dremel是一篇单独的论文，但总体思想像图3(a), 3(b)中那样，将每个叶字段作为单独的列，每一列都与两个整数相关联，分别是repetition level(R)和definition level(D).
- R是说我这是不是一行新的数据，R=0是说这是新的一列，而R=1是说这是后续值，比如说在tag中，b的R就是1，因为它是和a在同一条数据中
- D其实代表的是某个值定义到了哪一层，比如说在first中，第二行中的D=1，是说这个数据没有first值但是定义了第一层的name值

其实他给的这个表的逻辑上应该是这样的

| first | last | tag |
| ----- | ---- | --- |
| Mike  | Lee  | a,b |
|       | Hill |     |
| Joe   |      | c   |

然后Parquet是列存储，所以first, last, tag是三列，只不过因为由于是Json，有层级关系，比如其实first和last是name下的，所以又加了R和D来

ORC采用的方式更直观，如图3(b), ORC采用了presence 一个布尔列来标记某一位置的值是否存在(是没有这个定义 还是 null), 通过length一个整数列来标记某个repeated数组有多长。比如在例子中的first，值列是`["Mike", "Joe"]`，p列式`[1, 0, 1]`，就很直观

所以说ORC的方式是给嵌套结构创建单独的列（比如例子中的name和tags）,而Parquet是通过R和D将这种结构信息展现出嵌套结构来

### Columnar Storage Benchmark
对Parquet和ORC进行性能和空间效率的压力测试
市面上常用的标准的OLAP基准测试，如SSB，TPC-H, TPC-DS都是均匀的数据集，而有些数据集像YCSB, DSB和BigDataBench允许用户设置倾斜度，但还是无法接近正式世界的数据集分布
所以作者他们设计了一个基于真实数据的基准框架来评价列式存储的数据集，他们定义了一些值分布的一些特征，比如排序程度，偏斜模式，然后从真实数据中提取这些属性，构造预定义workload模版，用来代表不同场景的应用负载，比如BI，ML。在使用的时候，只需要配置文件来配置比如偏斜程度，是否排序等，然后workload生成器就会生成模拟数据
#### Column Properties
这里介绍了列值分布的核心属性，有
- NDV Ratio: 不同值的比率
- Null Ratio: 空值的比例
- Value Range: 数值或字符串的跨度
- Sortedness: 有序程度
- Skew Pattern: 倾斜程度

**NDV Ratio**
$$
f_{cr} = \frac{NDV}{N}
$$
NDV -- number of distinct values, 是不同值的个数，换句话说其实就是列表中的选项的选择个数，N是总记录数 -> 很多东西都是NDV低的，比如中国这么多人，但是省份就只有那么多选项，NDV低代表着所有记录的重复值多，可能就更适合Dictionary Encoding, RLE...
**Null Ratio**
$$
f_{\text{nr}} = \frac{|\{ i \mid a_i \text{ is null} \}|}{N}

$$
空值可压缩
**Value Range**
数值关注其平均值和方差，当然范围小的占位可能更少，更适合使用bitpacking
**Sortedness**
排序程序 -- 评估对RLE, Delta, zone map的影响
作者他们提出了一种度量，对默认512个条目，计算排序度指标
$$
\begin{align*}
\text{asc} &= \left| \left\{ i \mid 1 \le i < n,\, a_i < a_{i+1} \right\} \right| \\
\text{desc} &= \left| \left\{ i \mid 1 \le i < n,\, a_i > a_{i+1} \right\} \right| \\
\text{eq} &= \left| \left\{ i \mid 1 \le i < n,\, a_i = a_{i+1} \right\} \right| \\
f_{\text{sortedness}} &= \frac{\max(\text{asc}, \text{desc}) + \text{eq} - \left\lfloor \frac{N}{2} \right\rfloor}{\left\lceil \frac{N}{2} \right\rceil - 1}
\end{align*}

$$
其中，asc为升序对数，desc为降序对数，eq为相等对数，通过这些来统计相邻元素之间是升序，降序或相等的次数
对于sortedness
- 取最大方向（升序或降序）加上“相等对”。
- 再减去一半的长度，目的是让乱序序列得到分数趋近 0。
- 分母是归一化项，使得全排序（如 strictly ascending）得分为 1。

**skewness**
使用了Zipfian分布(presudo-zipfian distribution)对列中的偏斜度进行建模
![Pasted image 20250729162558.png|500](/img/user/accessory/Pasted%20image%2020250729162558.png)
数据偏斜分为四类

| Skew Pattern  | s 值范围    | 描述                     |
| ------------- | -------- | ---------------------- |
| Uniform       | s≤0.01   | 每个值出现的概率近似相等，类似随机分布。   |
| Gentle Zipf   | 0.01<s≤2 | 温和的偏斜，有热门值，但长尾仍占据很多空间。 |
| Hotspot       | s>2      | 高度偏斜，极少的热门值覆盖几乎整个列。    |
| Single/Binary | 特殊情况     | 整列只有一个或两个不同值的极端例子。     |

当然，越偏斜 压得会越好

#### Parameter Distribution in Real-World Data
作者通过真是世界的数据集来描述了上面提到的参数
- 商业智能类数据集: Public BI Benchmark，来自Tableau的真实数据和查询，包含206个表
- 数据仓库型OLAP工作负载: ClickHouse, 来自ClickHouse 官方教程中的示例数据集
- 机器学习训练数据集: UCL-ML, UCI 机器学习数据集集合, 用于ML训练的622个数据集的集合。作者选择了9个大于100 MB的数据集。所有都是数字数据，不包括非结构化图像和嵌入
- Yelp: Yelp 平台的商户信息、用户评价和用户基本资料
- LOG: EDGAR（美国证券交易委员会公开数据库）网站上的访问日志，结构化日志记录，如时间戳、用户 IP、请求路径、状态码等
- Geonames: 全球地理信息，包括国家、城市、坐标、行政等级
- IMDb: 电影基本信息（标题、演员、导演、语言、评分等）

结果: 这是堆叠柱状图可以从网上学一下怎么看 
![Pasted image 20250729171912.png](/img/user/accessory/Pasted%20image%2020250729171912.png)
- NDV Ratio: 超过80%的整数列和60%的字符串列其NDV radio<0,01 -> 大多数数据的可选的种类非常少，换句话说就是数据高度重复，即使是浮点列也有 60% NDV ratio < 0.1 -> 大多数真实数据都适合Dictionary Encoding
- Null Ratio: Null比例整体偏低，String相比Integer和Float来说null会多一点 但也就50%左右 -> 编译器不必多度NULL优化
- Skwe Pattern: 极少Uniform, 意味着值不是均匀分布的，Gentle Zipf和Hotspot占绝大多数 -> 列示格式需要高效处理这两种偏斜的情况
- Sortedness: 极化分布，不是排好序就是完全无序 -> Delta Encoding和FOR可以发挥好的作用
- Int Value Range: 长整数不多 -> Bitpacking会很好的起作用
- String Length: 长字符串占比少 -> Run-Length和Dictionary Encoding

作者真实世界的公共对象存储做出了分析，也是这么个结果

#### Predefined Workloads
根据真实数据集，做了五个预定义的工作负载，用于前面介绍的配置参数和生成数据
- bi(Business Intelligence): 基于公共BI基准
- classic: 基于IMDb，Yelp和Clickhouse样本数据集的子集
- geo(地理信息): 基于Geonames、ClickHouse的Cell Towers和Air Traffic数据集
- log(日志): 基于XML和来自Clickhouse的机器生成的日志数据集
- ml（机器学习): 基于UCL-ML训练集

每种工作负载各种数据类型占比
![Pasted image 20250729174813.png](/img/user/accessory/Pasted%20image%2020250729174813.png)
每个工作负载的参数设置
![Pasted image 20250729174903.png](/img/user/accessory/Pasted%20image%2020250729174903.png)
calssic工作负载: NDV比例高，偏斜系数高，字符串占比大
log工作负载: NDV比例低，重复值多，整体排序较好
classic和geo中字符串密集，log和ml浮点密集
混合负载: 50% bi + 21% classic + 7%geo + 7%log + 15%ml

除此之外还设置了查询选择率(Selectivity)设置, bi和classic具有高选择性，因为这些场景通常涉及大扫描; geo和log中使用低选择性，因为它们的查询请求来自小地理区域或特定时间窗口的数据

### Experimental Evaluation
goal: 实验 -> 知道下一列存储格式的设计
以此介绍
- 5.1 Experiment Setup(实验装置)
- 5.2 Benchmark Result Overview
- 5.3 - 5.7 设置对照实验来检查格式的关键组件
- 5.8 ML工作负载
- 5.9 GPU能力 (不过在Lecture02中只考虑CPU)

#### Experiment Setup
实验运行在 Amazon EC2 的 `i3.2xlarge` 实例上。这是一种高 IO 性能实例，配置如下：
- 8 核 CPU（Intel Xeon E5-2686 v4）
- 61 GB 内存
- 1.7 TB 的本地 NVMe 固态硬盘
- 操作系统为 Ubuntu 20.04 LTS

使用 Apache Arrow v9.0.0 来生成测试所需的 Parquet 和 ORC 文件
两种格式的参数配置
- Parquet
	- row group: 1m rows
	- Dictionary page size: 1MB
- ORC
	- row gourp: 64MB
	- Dictionary Encoding: NDV-radio: 0.8

使用Snappy压缩
Parquet 使用的是 Arrow C++ 集成实现， 但为了评估最低粒度的Zone Map，用的Rust实现的Parquet(v32)
ORC 使用 v1.8
编译器版本是 g++ 9.4

评估指标
- File Size
- Scan Performance With Filters

三次取平均值
不能使用Arrow Table作为评估基准: Parquet 与 Arrow 紧密集成，解码时可以直接生成 Arrow 的结构（如 DictionaryArray）。ORC 则不行，它先要转成中间结构 `ColumnVectorBatch`，再转成 Arrow 表，多了转换成本。  ->  采取原始扫描策略，系统使用固定大小的内存缓冲区，读取每块数据后写入缓冲区

#### Benchmark Result Overview
1. 默认配置下，对前面定义好的五种workload
2. 对每种workload, 生成一个20列，1m row的表，并保存为Parquet或ORC文件
3. 对文件顺序扫描进bufferPool，记录扫描所用的时间
4. 清空buffer cache，执行30个查询，查询filter的选择率或者说命中率按照前面的table4的数据设置的

![Pasted image 20250730172021.png](/img/user/accessory/Pasted%20image%2020250730172021.png)

**File Size**
no clear winner
在log和ml的workload上，Parquet文件更小: 因为Parquet对float应用了Dictionary Encoding，实际上这些数据的NDV radio较低(Figure 5a中展示了), 所以适合字典压缩，而ORC只对字符采用了Dictionary Encoding
在classic和geo上，ORC的文件更小，因为这些数据字符串多 ORC对字符串处理的好
**Scan Time**
从6b来看 Parquet的扫描性能更好
因为Parquet对整数的方式更为轻量，二次压缩默认是Bitpacking，当重复次数为8时，才会启用RLE（前面提到了因为Parquet对整数采用了Dictionary Encoding, 所以丧失了POR和Delta Encoding），而ORC前文也提到了, 对整数采用了4种编码方式，就需要额外记录每个子序列的Encoding的方式，并且只要重复超过3就用RLE，但是事实上，RLE阶码难以通过SIMD进行优化(SIMD（单指令多数据）是一种可以并行加速处理的 CPU 指令机制, 而RLE因为每个值的重复次数不同，导致数据展开后对齐方式复杂)
**Select Time**
Parquet 在大多数 workload 中查询更快，唯一例外的情况是geo
大多数Parquet快的原因跟scan time是一样的原因
例外出现的原因是ORC的zone map粒度更低，而Parquet的zone map粒度更粗，而geo的特点是NDV ratio高，但谓词选择低，所以更适合低粒度 跳过的更多

#### Encoding Analysis
![Pasted image 20250730175540.png](/img/user/accessory/Pasted%20image%2020250730175540.png)
这是对1m行，20列相同数据类型的数据，通过控制变量法，改变NDV Ratio, Zipf, Sortedness, Value Range的大小对文件大小的影响(暂时禁用了Parquet和ORC的块压缩)
- 对于Integer
	- NDV Ratio: 在NDV低的时候Parquet更好，NDV高的时候ORC更好，因为Parquet对数值类型有Dictionary Encoding，这在different values少的时候是很有效果的，但随着NDV增加效果会变弱，Dictionary也会变大，就不如ORC了，但是Float除外hh
	- Zipf: 随着数据更偏斜(重复数据更多)，压缩效果越好，但是ORC会更早的体现出压缩效果来，因为ORC的RLE在三个重复值就启用，而Parquet要8个
	- Sortedness: ORC优，其实前面也提到过，因为ORC多了Delta Encoding和FOR
	- Value Range: 对于数值，Parquet的文件大小是稳定的，因为Parquet对整数应用字典编码，并仅对字典代码使用Bitpacking + RLE，而ORC的文件大小随着值范围增大而增大，是因为ORC直接对原始数据进行编码
- 对String都差不多太多，因为都差不多是Dictionary Encoding
- 对FLoat来说Parquet都表现的好，因为字典编码对真实的世界中的浮点数非常有效

对未来的建议：虽然现实世界的NDV比率低，但是Parquet对虽有的数据都默认字典策略，作者认为是否可以设置一个阈值，来选择其他的算法

![Pasted image 20250730181559.png](/img/user/accessory/Pasted%20image%2020250730181559.png)
这是解码速度实验的结果,只看a和d啊，a和d是未使用快压缩的结果，还是采用了上一个实验的数据，使用full table scan(全表扫描), means不使用谓词下推等，测了IO时间和解码时间
8a现实的是在整数列和字符串列上，Parquet 的解码速度快于 ORC，原因:
- Parquet 更依赖快速的 Bitpacking（位打包编码），相比之下不那么激进地使用 RLE
- Parquet的整数的选择的机制少，ORC的预测算法并不好，实验说明，在解码过程中，ORC 在四种整数编码算法间频繁切换，导致的分支预测失败次数是 Parquet 的 3 倍，如下图![Pasted image 20250730181943.png](/img/user/accessory/Pasted%20image%2020250730181943.png)
- ORC具有比Parquet多4倍要解码的序列![Pasted image 20250730182340.png](/img/user/accessory/Pasted%20image%2020250730182340.png)

Parquet 使用 SIMD 指令和代码生成优化了解码中的 bit-unpacking（解包）过程，以减少不必要的分支判断
虽然ORC的算法选择并不友好，而且解码序列多，但是int类型Parquet只占了一丢丢优势说明Parquet额外的Dictionary的解码有一定的开销

![Pasted image 20250730182650.png](/img/user/accessory/Pasted%20image%2020250730182650.png)
为了进一步展示 Bitpacking 和 RLE 在性能与空间上的权衡，论文中又构造了一个字符串列，其中每个字符串值在列中连续重复 `r` 次，而图9展示的是Parquet和ORC在不同重复次数下的解码速度和文件大小
实验表明，对于短重复序列，RLE 的解码时间比 Bitpacking 更长。随着重复次数增大，RLE 和 Bitpacking 之间的性能差距迅速缩小。而文件大小方面呈现相反趋势：RLE 的压缩率远优于 Bitpacking。
对于浮点数，ORC 的解码性能优于 Parquet，因为 ORC 不对浮点值使用任何编码算法。尽管ORC中的浮点列占用的空间比Parquet中的字典编码列大得多（如图7所示），但使用现代NVMe SSD节省的计算量超过了I/O开销。

**未来的建议**
- 关注解码速度，并不是压缩的越好就越好，要保持开放简单，编码方式多了并不好
- 选择在真实数据上更有优势的Encoding算法
- 未来的格式可能不想使任何轻量级编码算法成为“强制性的”，因为IO的影响越来越小

#### Block Compression
研究快压缩对格式的性能和空间权衡 -- 比较了Snappy, Zstd的压缩算法 -- 重复了之前的解码速度实验
结果说明Snappy速度快，压缩率一般；Zsts压缩率高，解压慢
与未使用快压缩的数据相比，能发现Zstd在所有数据类型上都比Snappy有更好的压缩比
还能说明块压缩对ORC中的浮点列是有效的，因为它们包含原始值，但对于其余列的压缩效果是有限的
然后论文进一步研究了在 AWS 上不同类型的存储设备中，对 Parquet 应用块压缩时带来的 I/O 好处和计算开销。
![Pasted image 20250731101815.png](/img/user/accessory/Pasted%20image%2020250731101815.png)
图中的横轴的存储层是按读取带宽递增排序，这个图是通过核心工作负载生成1m行20列的表，在禁用和启用Zstd情况下的结果
对于结果来说，对于慢速设备(如st1)，使用zstd的整体时间变短了，因为在慢速设备上IO占支配地位，压缩使得IO变少（减少了传输的次数和总大小）
但对于快速设备如SSD和nvme来说，使用zstd的时间反而不用不使用快压缩，这是因为在快速设备中，IO时间并不再占主导作用，反而是解码时间占主导作用，而快压缩的解码又需要时间
但是对于S3来说，虽然它带宽不错，但是访问延迟高，读取一个Parquet文件的过程可能涉及到多个http请求，所以IO还是占主导因素，而压缩使得文件更小，请求更小
#### Wide-Table Projection
![Pasted image 20250731103339.png](/img/user/accessory/Pasted%20image%2020250731103339.png)
在这个实验中，生成10k行，选择10列进行投影
结果发现随着wide-table的列数或者说属性增加，元数据解析时间线性增长 -- 原因是元数据必须逐个解码 -- Parquet和ORC中的页脚结构不支持有效的随机访问。模式信息在Thrift（Parquet）或协议缓冲区（ORC）中序列化，仅支持顺序解码 -- 所以说读取所有列就需要顺序扫描前面的所有列直到找到这10列
另一个发现是当表变得wide的时候，ORC的性能下降，原因是因为ORC的row group是按size定义的而不是行数，所以说表越宽，每个行组的行就越少，行组就越多，需要随机访问扫描的元数据就多
-> Parquet和ORC的元数据布局对宽表投影并不友好: 未来希望能够支持快速定位目标列的元信息，而非线性遍历

#### Index and Filters
测试了 Parquet 和 ORC 中内建的 Zone Map 和 Bloom Filter 对查询加速的效果，查询条件（谓词）具有不同的选择性（selectivity）
结果总的来看：Zone Map和Bloom Filter对低选择查询效果很好
其中Zone Map对聚类良好或者有序的列效果良好，Bloom Filter适用于等值查询

#### Nested Data Model
为了 纯粹测试嵌套结构对性能的影响，使用 float 类型作为数据内容，并关闭了编码与压缩，避免其他优化干扰测试结果
![Pasted image 20250731104710.png](/img/user/accessory/Pasted%20image%2020250731104710.png)
构造了12a的结构
结果表明
随着嵌套深度的增加，Parquet膨胀的更快 -- 原因是因为Parquet使用的是Dremel编码模型，嵌套越深，每个叶节点编码越长
转换成Arrow表的时间 Parquet更快 -- 是因为ORC需要中间结构
在 扫描时解码嵌套结构的开销，ORC 的开销随深度增长明显变大，Parquet 的增长相对更缓 -- 是因为ORC必须重构每一层struct和list的结果；而Parquet有一连串编码只需解析 叶子节点的数据 + def/rep levels
建议
- 未来文件格式应更关注如何降低嵌套数据的解码与结构转换开销
- 可优化的方向：
    - 改进 in-memory 转换机制
    - 结构化嵌套信息的编码方式（支持随机访问或更紧凑编码）
    - 混合 def/rep 和 struct 模型的自适应嵌套策略

#### Machine Learning Workloads
ML workloads下数据特点:
- 包含原始数据（如图像 URL、文本）和元数据（如图像尺寸、标签）；
- 包含向量嵌入（vector embeddings）：通常是浮点数向量，用于相似度搜索（similarity search）；
- 这些数据常以 嵌套 list 格式 存储在 Parquet 文件中


##### Compression Ratio and Deserialization Performance with Vector Embeddings

![Pasted image 20250731105624.png](/img/user/accessory/Pasted%20image%2020250731105624.png)
实验选取30 个含向量嵌入的数据集，来自 Hugging Face 的 top downloaded 和 trending 榜单，存储为4中结构：Parquet, ORC, HDF5(层次化存储), Zarr(分块存储，面向科学计算)
实验发现所有格式的压缩率都不理想，即使是为数值数据优化的 Zarr，也未能显著压缩向量嵌入 -> 说明现有通用的压缩方法在浮现向量压缩上效果有限
实验还发现Parquet和ORC扫描更慢，Zarr的扫描更快 -- 这是因为Zarr 将固定长度向量嵌入 按块分割（grid chunks），支持 并行解码与扫描；而Parquet和ORC仅支持row group范围内顺序解码

**Discussion**
现有的列式格式并未为“向量嵌入”这类 **ML-specific 数据结构** 做优化
所以未来要引入面向浮点向量压缩的专用数据类型/结构并增强并行解码能力，提高大规模 ML 数据加载效率

##### Integration with Vector Search Pipeline
![Pasted image 20250731110816.png](/img/user/accessory/Pasted%20image%2020250731110816.png)
实验选取LAION-5B 图文数据集前 1 亿条用于构建向量库，使用 FAISS 向量索引（内存中自动调优），对剩下的嵌入做 top-10 相似查询。先检索向量 ID，再从 Parquet/ORC 中读取图像 URL 和文本。使用批量查询（batch）减少读取放大（read amplification），在本地NVMe SSD和AWS S3上分别实验
在本地，ORC的查询速度快于Parquet，因为ORC使用更细粒度的zone map
而在S3，Parquet的效果更好，是因为ORC 查询过程中产生了 ≈4 倍 S3 GET 请求，ORC 的 zone map 分布在各个 row-group footer 中，Parquet 的 zone map 集中在文件 footer，访问开销低

-> 在对象存储中减少小 I/O 操作，如聚合 zone map结构; 细粒度的索引

##### Storage of Unstructured Data
许多深度学习数据集包含非结构化数据，比如图片、音频、视频；一种常见做法是只存储外部 URL（如 LAION-5B），优点是文件小；但需要大量 HTTP GET 请求，网络开销大且不稳定且URL可能失效
-> 因此，将非结构化数据存储在同一个文件中是有益的 (给出了论文支撑证明这个idea)

![Pasted image 20250731110829.png](/img/user/accessory/Pasted%20image%2020250731110829.png)
使用LAION-5 B数据集在Parquet上对此进行了评估，其中图像URL被原始二进制文件替换。生成的Parquet文件为13 GB，包含219 K行，存储在NVMe SSD上
使用五个不同的过滤器（filter_0 - filter_4）执行扫描，它们的选择性分别为1、0.1、0.01、0.001和0.0001
开启了列块并行读取和预缓冲
图15 a显示了投影图像列时的查询时间(即需要读取大二进制列)，而图15 b显示了仅投影表格列时的查询时间
结果说明
带图片投影较小的row group效果好，because row group 小 → row group 数多 → 可并行异步读取多个大二进制块
对于仅投影表格列的情况，较大的row group效果好，因为ow group 小导致压缩效果变差，结构化数据占用更多空间，进而IO开销大

-> 将大二进制数据与结构化数据混合存储，并用默认的 row group 大小，效率很低
-> 未来应该区分，但在逻辑层保持统一接口

后面GPU先不看了