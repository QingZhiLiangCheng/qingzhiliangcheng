---
{"created":"2025-09-14T09:46","updated":"2025-09-14T20:06","dg-publish":true,"permalink":"/DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 03 Data Formats & Encoding II/","dgPassFrontmatter":true,"noteIcon":""}
---

![[03-data2.pdf]]

### Last Lecture：Nested Data
这是上节课没讲完的部分
现实世界的数据集，大量使用JSON格式，在Google内部，广泛采用Protocol Buffers
这些都是嵌套数据
如果仅仅将JSON文档作为文本或者blob存在列中，我们可以运行JSON函数提取结构，但是会失去Pax布局和向量化执行的所有优势
相反，我们想要做的是将每个元组的JSON文档拆分或者展开，并将该文档中的路径存储为单独的列
将这些存储到列式存储格式中有两种主要方法
- Record Shredding(记录粉碎)
- Length + Presence Encoding (长度+存在编码)

#### Shredding
Shreding的想法是把每个路径都作为单独的列存储，然后附带关于路径的额外的元数据
- Definition Level: 在到达一个属性的路径上，有多少个可选/重复的元素被定义了（即存在）
- Repetition Level: How many times a structure has been repeated 一个结构被重复了多少次

![Pasted image 20250914111840.png|500](/img/user/accessory/Pasted%20image%2020250914111840.png)

![Pasted image 20250914111759.png|500](/img/user/accessory/Pasted%20image%2020250914111759.png)

注意这个Definition Level，它值得是到达这个属性的路径 只算了不required的，也就是repeated和optional的个数走了多少步
而且要注意所有的属性都要看一下，也就是说要记录null值，注意null值的definition由于这个属性并不存在，所以并不算他自己，其实很好理解
事实上，对每一个属性都建立一列，就是`DocId`, `Name.Url`, `Name.Language.Code`, `Name.Language.Country` 实际上就是给每个叶节点建立了一列表
事实上definition通过这个表名，然后看一下每个属性是不是required的就能算出来
我们从第一个Name开始看，第一个`Name.Language.Code: 'en-us'`,记在对应表，由于这是第一次出现所以repetition置为0, definition刚刚我们分析了，注意code是required的,所以definition为2
下一条是`Country:'us'` , 头一次出现为required为0，definition为3
然后后面又出现了一个Language，但这个Language里面只有一个`Code:'en'`, 所以在`Name.Language.Code`里面加一条`en,1,2` 注意这里的repetition为1，表示前面出现过
不过要注意Language其实还应该有一个optional的Country，这也要记录，记录值为null，由于事实上这个Country并不存在，所以d为2就可以，所以记录`NULL,1,2`
事实上后面都是这个逻辑

again, shredding是为了利用PAX存储格式和向量化的优势
重新拼接会带来很大的开销吗？ Yes！
但优势在于，查询的时候快啊，直接去拿数据就行了
虽然SQL标准里面包含了JSON类型的格式，但那只是前端程序员所看到的，在数据库系统的内部，需要根据需要自由的存储数据，而Dremel之所以采取这种方式，是因为他们希望针对仅沿路径进行查找这一常见情况进行优化，为了避免的是所有的都是二进制大文件(Blob)，每次都需要解析

事实上Repetition Level更精准的定义 指示的是，当一个新值出现时，它是在哪个嵌套深度的重复列表中开始了一个新元素
比如上面的Name.Language.Code里面的en严格r应该是2，代表着是在第一个Name里面的Language重复
```python
# R = 0: 标志着整个循环的开始
for name in document.names:  # <-- 每次进入这个循环，都是在 Level 1 上重复 (R=1)
    
    # ... 处理 name 内部的字段 ...

    for lang in name.languages: # <-- 每次进入这个循环，都是在 Level 2 上重复 (R=2)
        # ... 处理 lang 内部的字段 ...
```

#### Length + Presence

![Pasted image 20250914132146.png|500](/img/user/accessory/Pasted%20image%2020250914132146.png)
- 长度(Length) 列：对于每个重复字段（数组/列表），创建一个额外的列来存储这个列表的长度。
- 存在性 (Presence) 列: 对于每个可选字段（optional），创建一个额外的布尔（boolean）列来标记这个字段是否存在

在上面的例子中，Name的第一个值3表示的是第一个DocId=10这个记录里有3个Name块，Name.Url对应的就是3个Name中url字段是否出现以及出现的值...
其实自我感觉这种方式更佳直观的感觉
但后来又研究表明，shredding的效果更佳

### Critiques Of Existing Formats
Parquet和ORC诞生在10多年前，这两个文件格式实际上是针对那时候的硬件时代设计的，但现在硬件已经发生了变化，网络也更快 
**Variable-sized Runs(可变长度的运行/段)**: 我查了一下，这个名词一般用在讨论数据压缩算法的时候，特别是RLE中, Variable-sized runs的意思是连续的值的长度是可变的，不可预测的。 这是 **Not SIMD friendly** 的
**什么是SIMD？ Single Instruction, Multiple Data(单指令，多数据)**
SIMD使得现代处理器能够对多组数据执行相同的操作，与之对应的是SISD(单指令，单数据)
![Pasted image 20250914134902.png|500](/img/user/accessory/Pasted%20image%2020250914134902.png)
假设对两个矩阵进行矩阵加法，SISD的做法是设置一个小型的for循环，遍历x和y中对应的元素一个一个相加
在SIMD的概念中，可以将待处理的数据分解成若干块，寄存器会被分成多个通道lanes，在这个例子中，假设存储的是32位整数，四个值放入一个寄存器中，所以我们拥有的是四个lane的128位寄存器，然后可以用一条指令对应相加
![Pasted image 20250914135300.png|400](/img/user/accessory/Pasted%20image%2020250914135300.png)
值得注意的是，这些寄存器始终是128位，256位或者512位(现在目前最大就是512，FastLanes这篇论文所提到的1024是虚拟的)，我们存储在每个通道中的每个元素都必须具有相同打下，所以我们在我们采取变长编码之后必须要将所有的内容转换为相同长度，相同大小才能加载到寄存器中
**Eager Decompression** 这些格式的另一个问题在于倾向于急于解压缩所有的内容，所以他们不会将字典暴露在数据库系统中的执行引擎，以允许直接在字典上进行查找；Parquet和ORC在迭代块时，返回的直接就是解压缩后的值，而如果使用块压缩，更是数据库不可见的
**Dependencies Between Adjacent Values** 这个问题出现在某些编码方案中，特别是Delta Encoding, RLE... 相邻块存在依赖关系， not SIMD friendly

**today's agenda**
- BtrBlocks(TUM)
- FastLanes(CWI)
- BitWeaving(Wisconsin)

### BtrBlocks (TUM)
#### Overview
BtrBlocks, a PAX-based file format，事实上可以认为是Parquet++
BtrBlocks的核心思想是采用比Parquet和ORC更加激进的编码方式
Parquet仅对字符串进行了字典编码，但并未对由此产生的代码尝试进行任何额外的优化；ORC在整数列上试图做的更加精细，至少在整数列上，试图判断应该以哪种方式压缩，但基本上是静态的启发式方法
BtrBlocks 通过或多或少的贪心算法，针对每个列快查看数据样本来确定最佳的编码方案，后面会讨论是如何生成这个样本的，然后他们会递归的尝试对编码的输出进行再次编码，这些做法事实上获得了Snappy和zstd这些快压缩的所有好处，同时能够更加原生的操作和解码列数据，其实不光是BtrBlocks，FastLanes其实也做到了这一点，因此其实我们能看到实际上paper中没有任何关于Snappy和zstd的内容
另一个有意思的事情是, BtrBlocks提出他们不希望将元数据，模式以及文件内容的相关信息存储在文件本身中，他们觉得这些内容最后留给管理服务处理

> 在这里我突然想起来了前两天我搜到DuckDB在2025年5月份发布了duckLake, 他们的思想跟这里差不多，他们觉得所有的metadata不应该通过传统的方式那样存在Json文件中放在对象存储中，他觉得应该使用数据库服务来管理元数据
> 这个想法是很相似的，只不过我觉得duckLake所说的metadata应该是更高层次的表格式之类的元数据，而BtrBlocks所说的元数据应该是Parquet文件中的有关行组等的元数据


这里老师提到了，他觉得BtrBlocks的这个想法破坏了可移植能力，原来将Parquet文件交给别人，这里面就包含了所需要解密内部数据所需的所有内容
老师说这可能更多的是关于哲学设计策略的争论，而非简单的评价谁对谁错
当然这些metadata可能作为额外的文件最后还是存储在了对象存储中(那还能存储在哪里呢？)，那其实对于我们来说从文件的fotter读和从单独的文件中摘取一部分 并没什么大区别，可能只是连续不连续IO的问题？

#### Encoding Schemes
- RLE/ One Value
- Frequency Encoding
- FOR + Bitpacking
- Dicitonary Encoding
- Pseudodecimals
- Fast Static Symbol Table (FSST)
- Roaring Bitmaps for NULLs + Exceptions

这里面大部分都学过了
这里简单的先说几个不太熟悉的，后面还会着重介绍FSST和Roaring Bitmaps
**One Values**: 实际上就是RLE的一个极端情况，整个数据块都是一个值，存储值和数量
**Frequency Encoding**: 找出数据中最常出现的值（Most Frequent Value），然后用一个位图（bitmap）来标记这个值出现的位置。剩下的非最频繁值则另外存储; 比如对于`[ "Google", "Direct", "Google", "Google", "Facebook", "Google", "Direct", "Google", "Google", "Baidu", "Google", "Google" ]`存储为三部分 `"Google"`，`101101011011`， `[ "Direct", "Facebook", "Direct", "Baidu" ]`
**Pseudodecimals** 定点十进制，基本思想是通过确定小数点的位置，将浮点数转换成整数，并存储整数版本
**FSST** 源于德国人和DuckDB团队，对字符串进行压缩，但与采用字典编码不同，字典编码是对整个字符串的替换，FSST是对字符串内部子串的替换，比如把众多url的https编码
**Roaring Bitmaps**是一种压缩位图的方法，用于处理空值和异常情况

选择算法的工作原理是要从列中收集一些样本数据
之前学过在ORC中，ORC使用了预读缓冲区来查看接下来的512个字节来选择压缩方式
BtrBlock做的是拥有整个列块，然后从该列块中均匀采样，并利用采样结果来决定这个特定的列快的最佳编码方案
也就是说BtrBlock是对列快为单位做的编码，而ORC由于512字节的原因，可能一个列快中会有多种编码方式？
不过问题就在于，不能仅仅通过跳远到不同位置随机采样，因为这会错过连续性的，重复的值；也不能仅抓取前100个值，这也会使得编码方案不佳
BtrBLock的做法是对于64k的值的数据库，跳跃的选取10个64值的段落，然后拼接起来 -- 这提供了空间的随机性，也保留了连续性
![Pasted image 20250914155125.png|400](/img/user/accessory/Pasted%20image%2020250914155125.png)
他们会测试所有的用于Integer的方法，而且他们表示，这大约只是压缩成本的2%作为额外开销
对于每一个输出会递归的尝试三次
![Pasted image 20250914155754.png|400](/img/user/accessory/Pasted%20image%2020250914155754.png)
对Double和Stirng也是类似的决策树

#### FSST
FSST源于[FSST: fast random access string compression: Proceedings of the VLDB Endowment: Vol 13, No 12](https://dl.acm.org/doi/10.14778/3407790.3407851)这篇论文, 2020
FSST的基本思路是用一字节代码代替频繁出现的最多八字节子串
他们生成符号表的方法很有意思，事实上，确定最少代码数并获得最大压缩效益的问题属于NP完全问题，因此，与其尝试他们提到的动态规划或者更多的复杂的方法，他们只是用了所谓的进化算法，也就是说，当你认为在构建符号表时找到了一个好符号，他们就会将其放入哈希表中，如果某个条目存在，将会被剔除。这不同于我们之前学过的线性扫描或线性探测哈希表，也不同于链式哈希表，在我们学过的这些哈希表中，如果发现槽位被占用，会有一定的规则继续去找空位
在这里，由于会直接剔除，所以一段时间后会得到一个大致合理的符号集合
哈希表暴露给了数据库

#### Roaring Bitmaps
这是如今在数据系统中频繁出现的一项炫酷技术 -- 咆哮视图？hhh
Roaring Bitmaps的核心在于这是一种位图索引，它根据比特位的局部密度，切换使用哪种数据结构来存储一个范围内的值。
Roaring Bitmaps的思想是把巨大的整数空间切分为固定的大小(一般是$2^{16}$)的小块，根据每个小块内部的数据密度，决定用哪一种方法
- 稀疏块，把这些仅有的数存入一个有序数组中
- 稠密块，位图 -- 相对应的地方存1

稠密的块还可以用RLE进行压缩
![Pasted image 20250914184353.png|400](/img/user/accessory/Pasted%20image%2020250914184353.png)
在课程中的例子稠密和稀疏的分界值是4096

![Pasted image 20250914184509.png|500](/img/user/accessory/Pasted%20image%2020250914184509.png)

事实上，BtrBlocks是想使用更激进的编码方式，来改变数据直接的相互依赖，比如FOR就会比Delta更好
但是BtrBlock事实上还是会生成变长的段，所以事实上还是not SIMD friendly的
### FastLanes
FastLanes并不像BtrBlocks那样是一个完整的数据格式，这仅仅是一种低级编码方案，通过重新排列元组的方式来实现更佳的数据并行性，从而确保或最大化在SIMD寄存器或SIMD指令中执行的有用工作量
这篇论文的惊叹之处在于，他们并未针对某一特定实例或配置的SIMD，或是某一CPU供应商进行设计，而是明确提出：创造自己的虚拟指令架构(Virtual ISA), 并用于1024的虚拟缓存器，而且可以很好的映射到现有的SIMD上
他们的核心在于Unified Transposed Layout
#### Unifed Transposed Layout
之所以能够实现重排某一列的元组实现更多的工作在SIMD中执行，是因为我们拥有物理层与逻辑层之间的独立性，关系模型是基于无序集合的
![Pasted image 20250914191518.png](/img/user/accessory/Pasted%20image%2020250914191518.png)

在解码的时候，会通过位移和其他操作，确保其恢复到正确的顺序
！只要没有开源系统能以这种方式存储数据，就没有人会这么做
实际上可能没法使用SIMD对这种游程编码进行解码 需要循环SIMD


### BitWeaving
前面所讲过的无论是Parquet, ORC, BtrBlock还是FastLanes，他们的核心思想都是在扫描某一列时，每一次都会完整的查看每个元组中的值，换句话说就是不会进行短路操作，所谓的短路操作其实就是看完前面的比特如果不匹配就会结束
所以其实这里的核心思想就是按位比较，及时跳出循环
硬件没法改变，硬件向我们提供API，他们在比较两个数的大小的时候不会看到第一位不同就放弃比较后面的位数
但是在我们的数据库中，我们或许可以比子集？
这个概念叫做bit slicing(位切片)
![Pasted image 20250914193459.png|500](/img/user/accessory/Pasted%20image%2020250914193459.png)

BitWeaving！
这里的基本思想是一种针对列式数据库的替代编码方案，其核心理念是位切片技术，但实施方式将确保最大化利用SIMD
他们提出了两种不同的编码方案
- Approach1: Horizontal -- 位级别上的行存储
- Approach2: Vertical -- 类似于位切片技术，但会进行特定操作，以便最大化SIMD

**Horizontal Storage**
![Pasted image 20250914194333.png|500](/img/user/accessory/Pasted%20image%2020250914194333.png)
在段落内部，按照顺序存储数据，从顶部到底部依次排序
这是展示的8位向量
除了将值存储为三个比特外，还有一个填充值，用来记录特定操作的结果，即真或假
![Pasted image 20250914195132.png|500](/img/user/accessory/Pasted%20image%2020250914195132.png)
优点在于仅需要三条指令即可评估
在对所有向量操作之后，我们需要重新组合起来，以恢复我们在列中满足谓词或设置为true的元组的偏移量
右移若干位
![Pasted image 20250914195702.png|500](/img/user/accessory/Pasted%20image%2020250914195702.png)
生成的这个叫做选择向量
但这个选择向量只是一串数字，想要输出到底哪几个向量符合有两种方法
- Approach1: Iteration -- 就是一个for循环
- Approach2: Pre-computed Positions Table -- 查表

![Pasted image 20250914200015.png|500](/img/user/accessory/Pasted%20image%2020250914200015.png)

![Pasted image 20250914200044.png|500](/img/user/accessory/Pasted%20image%2020250914200044.png)

**Vertical Storage**
![Pasted image 20250914200212.png|500](/img/user/accessory/Pasted%20image%2020250914200212.png)
会浪费一点空间，Segement2后面的需要填充起来
![Pasted image 20250914200258.png|400](/img/user/accessory/Pasted%20image%2020250914200258.png)

![Pasted image 20250914200327.png|400](/img/user/accessory/Pasted%20image%2020250914200327.png)
但是算的快，因为可以短路操作，在把第一位比较玩之后就只看对应的t0, t3, t6了，其他的地方就放0了
最后没有为2的hh
