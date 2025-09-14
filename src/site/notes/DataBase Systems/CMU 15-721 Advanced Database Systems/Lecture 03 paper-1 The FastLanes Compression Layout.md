---
{"created":"2025-09-02T08:50:29.407+08:00","updated":"2025-09-14T09:40","dg-publish":true,"permalink":"/DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 03 paper-1 The FastLanes Compression Layout/","dgPassFrontmatter":true,"noteIcon":""}
---

![[Lecture 03 Paper 1 - The FastLanes Compression Layout.pdf]]

通篇读下来会发现事实上FastLanes的核心在于
- FLMM1024 虚拟的1024-bits 寄存器 -- 现在SIMD指令集有128位，256位，512位，由于SIMD指令集架构的宽度的不同，适合不同指令集的布局在另一个指令集中可能不会发挥最高的性能，文章中是论证了为什么宽布局->窄硬件简单 而 窄布局->宽硬件困难，所以事实上FastLanes的做法是选择了一个现在还不存在的位数 - 1024：一方面可以很好的支持现在的各种SIMD指令集，另一方面可以面向未来的1024指令集
- 紧密布局和解包 -- 事实上，在布局中，用了bit-packing，为了获得压缩的效率，有的bit-packing后的数据可能横跨两个lane，但论文中通过巧妙的Shift和Or的组合，最简单的指令高效地处理之前我们看到的“跨字拆分”的数据，实现了高效率解码
- Unified Transposed Layout(统一转置布局)  -- 这也是FastLanes的核心 -- FastLanes通过统一转置布局打破了原来编码方式中的顺序依赖，来达到更好的并行效果 ； 更巧妙的是，统一转置布局对不同的通道宽度都适用

这篇paper事实上是建立在我之前学习的基础上的
- Vertorized execution(向量化执行): 最初是在[[DataBase Systems/CMU 15-445：Database Systems/Lecture 12 Query Execution Part 1\|Lecture 12 Query Execution Part 1]]中学到的, vertorized execution事实上是Iterator Model(火山模型)和Materialization Models的一种综合, 简单来说其实就是一批一批的处理。事实上, 之前在[[DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats\|Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats]]中也提到过，现在的所谓的列式存储格式(PAX)事实上是先分成了row group然后再分成了column chunk，他们本身就是适合Vertorized exection的
- Vertorized decoding: 对于decoding的知识，最早是在[[DataBase Systems/CMU 15-445：Database Systems/Lecture 05 Storage Models & Compression#column-level\|Lecture 05 Storage Models & Compression#column-level]]中学的，在上一篇paper[[DataBase Systems/CMU 15-721 Advanced Database Systems/Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats#Encoding\|Lecture 02 Paper-1：An Empirical Evaluation of Columnar Storage Formats#Encoding]]也是分析了Parquet和ORC中不同的编码方式: Parquet对Integer, Float, String都积极使用DICT(Dictionary Encoding), 而ORC只对String使用了DICT; 也正因为Parquet对Integer使用了DICT，所以对Integer的二次压缩只有RLE和Bitpacking；而ORC对Integer的编码使用了RLE, Bitpacking, Delta Encoding, POR
- Compressed execution: 这其实算是一个想法 -- 这个想法是说下一代数据库不应该急切地将列解压为其SQL类型 -- 而是支持一个类似中间类型的东西 -- Procella、Velox和DuckDB这样的现代系统支持压缩向量，在这种向量中，数据既能被随机访问，又仍然保持部分压缩状态
- SIMD -- 这是之前不了解的一个概念 -- Single Instruction, Multiple Data(单指令，多数据) -- 实际上这是一种并行计算技术 -- CPU用一条指令同时对多个数据执行相同的数据 -- 可能每一个SIMD寄存器中会存多个数据，然后对应可以执行相同操作
- Lane(通道) -- 是指一个宽的 SIMD 寄存器被逻辑上划分成的多个独立的、较小的部分，每个部分用来存放一个数据元素 -- 对应通道进行相同的操作那样子

### 为什么设计了FLMM1024 虚拟的1024-bits 的寄存器？
之所以叫虚拟，是因为目前还没有1024bits的寄存器，再过去三十年里，SIMD寄存器翻了三倍，从128到256到512bits，设计1024一方面是为了迎合未来，另一方面是论文中提到了 宽布局在窄寄存器上没有问题，但是窄布局在宽寄存器上是低效的

### 宽布局在窄寄存器 & 窄布局在宽寄存器上
要理解这个问题，我们可以从4-way interleaved layout理解，首先所谓的4-way interleaved layout 是指在内从中按照4个lane分开存，这避免的是原来的顺序比特加载到寄存器中分成4个lane的高额的PERMUTE操作(在不同 SIMD 通道之间重新排列数据)和BITSHUFFLE操作(按位重新排列数据，比如把不同元组的位重新组合到一起)
事实上4-way interleaved layout是非常适合128bit寄存器的
考虑一下128bit，T=32bit (T: BitWidth of a physical type, 实际上就是lanes的大小), 4lanes，W=3bit(W是指数据通过Bit-packed压缩后, 一个数据为3bit) ,那么事实上内存中是这么存的
```
lane0: v0, v4, ... , v36, 
lane1: v1, v5, ... , 
lane2: v2, ...
lane3: v3, ...
```
这里注意放到v36实际上会占到30bit，那么这里就有一个问题，如果继续放v40，就只能放2个bit，就需要跨到两个lane中 ; 当然也可以空着；也就是说这里事实上有两种方案，一种是空着，优先考虑性能; 另一种是拆分，优先考虑空间利用率，但我觉着还得用跨通道的指令吧
其实后面1024用了方案2，而且很巧妙的组合了两个基本指令，避免了跨通道的指令
但是对于这个4-way interleaved layout用到256位寄存器上，并行性就不够了 -- 因为这里实际上是按照下标`lane_bit_offset = floor(k / number_of_lanes) * W`把数据分成了四组存在了四个地方，所以对于256位的8 lanes，只能放到每个寄存器的前4个lanes，后4个空着，从而并行度不够
当然这里其实我也在考虑 是不是可以在放一个4-way interleaved layout扔到后4个lanes，我感觉可以，只不过这里由于将数据存在了4个地方，所以如果想放到后面4个其实又涉及到了移动lane的操作
但事实上宽布局在窄寄存器上是没有问题的，就是多放几波的样子

![Pasted image 20250913202939.png](/img/user/accessory/Pasted%20image%2020250913202939.png)

这是paper中的Figure1，W=3bits, T=8(8的选择是为了能够最大化解码性能选择的能容纳W的最小通道宽度，当时4更下，但是8bit是一个字节，是计算机的一个基本单位)
这个图里是进行了拆分的

### ISA指令集架构
在虚拟的1024寄存器上，建立了通用的指令集 -- 之所以能通用，是因为用的都是简单操作

### 解包
```cpp
uint<8> MASK1 = (1<<1)−1, MASK2 = (1<<2)−1, MASK3 = (1<<3)−1;
FLMM1024 r1, r0;
r0 = LOAD<8>(in+0);
r1 = AND_RSHIFT<8>(r0,0,MASK3); STORE<8>(out+0,r1);
r1 = AND_RSHIFT<8>(r0,3,MASK3); STORE<8>(out+1,r1);
r1 = AND_RSHIFT<8>(r0,6,MASK2);
r0 = LOAD<8>(in+1);    STORE(out+2,OR<8>(r1,
	 AND_LSHIFT<8>(r0,2,MASK1)));
r1 = AND_RSHIFT<8>(r0,1,MASK3); STORE<8>(out+3,r1);
r1 = AND_RSHIFT<8>(r0,4,MASK3); STORE<8>(out+4,r1);
r1 = AND_RSHIFT<8>(r0,7,MASK1);
r0 = LOAD<8>(in+2);    STORE(out+5,OR<8>(r1,
	 AND_LSHIFT<8>(r0,1,MASK2)));
r1 = AND_RSHIFT<8>(r0,2,MASK3); STORE<8>(out+6,r1);
r1 = AND_RSHIFT<8>(r0,5,MASK3); STORE<8>(out+7,r1);
```
![Pasted image 20250913205127.png|400](/img/user/accessory/Pasted%20image%2020250913205127.png)

这是解码的过程
对于一般的没有跨lane的数据，就是利用了右移和掩码，因为掩码的存在，右移到另一个lane的内容直接就被消了
而对于跨lane的数据比较特殊，通过分别右移和左移给组合起来了

### 转置布局
之前提到的压缩顺序实际上相邻的值会放到寄存器相邻的通道中
但事实上我们用的encoding方法是Delta或者FOR, 实际上是依赖于前面的值的，实际上还要跨通道，所以提出了转置的这种布局
![Pasted image 20250913212719.png|400](/img/user/accessory/Pasted%20image%2020250913212719.png)

通过转置，相互以来的值放到了同一个通道里

### 统一转置布局 -- 瓦片
在上面提到的转置布局中，依赖T这个值，换句话说，T越大，lane就越少，T越小，lane就越多
那么事实上我们数据库中的不同列的数的大小是不同的
一方面是说由于T不同，所以在内存中布局不同，在组成整个元组的时候就很难对应，比如对于128的寄存器来说，32位的值4路并行的存储顺序会是`age[0], age[4], age[8], ..., age[1], age[5], age[9], ...`，而对于16位的值8路并行的存储顺序会是`count[0], count[8], count[16], ..., count[1], count[9], count[17], ...`
为了折中，只能选择相同的布局 那么count就只能重排
但是在载入SIMD的时候，由于位数，SIMD决定使用8路并行，但是只能放入前四个lane
![Pasted image 20250914090741.png|500](/img/user/accessory/Pasted%20image%2020250914090741.png)

为了解决这种问题，引入了一个统一转置布局，他的核心在于一个基本单位叫做tile(瓦片)
这里的核心在于，不再是简单的按步长跳跃，而是将1024个值划分为8个 `8x16` 的瓦片，这样在内存中就会有一个统一的布局，实际上都是按照`04261537`这个顺序存储的
通过 `04261537` 这个巧妙的顺序，它保证了当窄数据类型需要一次加载多个瓦片的数据时，加载进来的不同瓦片的数据在逻辑上是相互独立的（比如都是起点），或者处于相同的依赖深度（比如都是第二步）。
![Pasted image 20250914092037.png](/img/user/accessory/Pasted%20image%2020250914092037.png)
具体来说，对于不同长度的数据类型，他的瓦片的大小是不同的，1024寄存器所能放的瓦片个数也是不同的，但是瓦片内的8行数据已经转置好了，存在依赖关系，而加载进来的不同瓦片，比如0426也是没有依赖关系的，但0和1(下一次加载到相同lane上的瓦片）有依赖关系
![Pasted image 20250914092345.png](/img/user/accessory/Pasted%20image%2020250914092345.png)

不过我自己感觉这里对于T=64 依然按照0426这样往里加载的话，需要额外算0和1啊，不过其实如果按0123这样往里加载就得考虑IO的问题(因为一定是按照0426这样存的)

### FastLanes-RLE
其实这里的核心问题在于经典的RLE的编码方式解码的时候需要循环，而循环意味着并行度差，不过我倒是觉着if的并行度更差hh，当然我记得有分支预测，不过是由风险的
所以paper提出了一个叫Fastlanes-RLE的方案，将RLE映射到了DELTA
![Pasted image 20250914093255.png|400](/img/user/accessory/Pasted%20image%2020250914093255.png)
这里的诀窍在于给index vector单调加1，这样一Delta就变成了一堆0和1，那直接压缩其实效果还挺好的

### 实验
...