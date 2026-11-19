---
{"created":"2026-09-30T15:14","updated":"2026-11-11T17:14","dg-publish":true,"permalink":"/CMU11785 Introduction to Deep Learning/Lecture 02 Neural Networks：What can a network represent/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2026-09-30T15:14","updated":"2026-11-11T17:14"}}
---

### Last Class
神经网络是一个box，接受输入并计算输出 -- 所谓的box 就是一个functions
这些function是什么？在人类机体中，这些function是由人类大脑计算的 -- 人工神经网络模仿人类大脑的机构
人类大脑是由叫神经元的计算单元网格组成 -- 模拟神经元所建立的人工神经网络的计算单元网格的单元叫感知机。 接受一推输入（可以是实数，可以是布尔的），计算这些输入的加权和，并将这个加权和与一个阈值进行比较，如果超过阈值就会激活。即$\sum w_ix_i>T?1:0$ 
这完全可以重写成$\sum w_ix_i -T \geq 0$ 。加上一个偏移量b=-T，把$\sum w_ix_i -T$记作z，然后通过一个阈值激活函数，如果是正为1负为0
所以首先核心要计算的就是仿射函数z；然后通过一个激活函数，激活函数可以选择不同的函数。
单个感知机是不够的 -> 我们需要构造多层感知机(multi-layer perceptron)
感知机可以输入布尔值，输出布尔值 -- 表示布尔函数
感知机可以输入连续值，输出布尔值 -- 二分类问题
感知机可以输入连续值，输出连续值 -- 回归表示连续函数
### Today's Agenda
更加深入的探讨人工神经网络能表示什么以及他们的局限性。会涉及到activations(激活), depth, width如何影响网络能够建模的内容
- What can neural networks represent. 神经网络能表示什么？
- What are the restrictions in terms of "depth", "width" and "activations"? 宽度 激活函数方面的限制

The multi-layer perceptron
首先需要了解一个概念："depth"
我们所学的网络都是图(graph)都是有向图。会有节点，边，输入源，输出汇。从源流向汇
图的深度是指从源到汇的最长路径的长度
什么是深度网络(deep network)? -- 深度大于2的图
![Pasted image 20260930184833.png\|500](/img/user/accessory/Pasted%20image%2020260930184833.png)
在上面这个图左边的网络中，底层的灰色的节点是源，顶层的节点是汇。深度为3，所以这是一个深度神经网络
What is layer(层)? -- 所有与输入具有相同深度的神经元形成同一层
![Pasted image 20261002090518.png\|400](/img/user/accessory/Pasted%20image%2020261002090518.png)
值得注意的是 红色是第一层 输入算第0层
The multi-layer perceptron(多层感知机)是感知机的层网络

today's agenda
- 讨论多层感知机作为通用布尔函数以及深度的需求
- 讨论多层感知机作为通用分类器以及对深度的需求
- 讨论多层感知器作为通用逼近器以及对深度的需求
- 讨论深度和宽度之间的权衡
### MLPs as universal Boolean functions
前面我们学过了AND, OR, NOT都可以用一个感知机实现 现在我们把AND, OR, NOT 推广到多个输入的情况，仍然只需要一个感知机，所以说感知机可以表示广义的布尔函数或者说通用布尔函数
![Pasted image 20261119185528.png\|446](/img/user/accessory/Pasted%20image%2020261119185528.png)
AND是条件成立时，才输出1。而这是一个通用的AND：条件是：前L个是1，其余都是0时才能触发。给前L个权重1，其余分配权重-1。这样满足条件的加权和是L。当其中任意一位发生反转的时候，所得到的加权和都会小于L，不会触发
-> 我也可以构建满足其他条件的布尔函数！ 比如前L个都是0 其余是1时才触发的布尔函数！

我们也可以构造至少K个输入是1就会触发的布尔函数：
![Pasted image 20261119190444.png\|427](/img/user/accessory/Pasted%20image%2020261119190444.png)
前面提到过异或的表示方法：需要用到多层感知机
![Pasted image 20261119190600.png\|331](/img/user/accessory/Pasted%20image%2020261119190600.png)
但实际上，有研究表明只要两个感知机也能表示异或：
![Pasted image 20261119190625.png\|326](/img/user/accessory/Pasted%20image%2020261119190625.png)
那么我们就可以用感知机来组合任意的布尔函数，所以叫做MLPs as universal Boolean functions -- 任意一个布尔函数，都会有与其对应的MLP
![Pasted image 20261119190902.png\|406](/img/user/accessory/Pasted%20image%2020261119190902.png)

那布尔函数的复杂度和网络的深度有什么关系呢？
为什么说任意一个布尔函数，都会有与其对应的MLP呢？
什么是布尔函数？实际上布尔函数本质上就是一个真值表
那我们实际上就能列举出所有的组合，对于每一种组合都会写出一个对应的子句也就是析取范式，将所有的情况做一个或运算就好了。每个子句需要一个感知机
![Pasted image 20261119191454.png\|422](/img/user/accessory/Pasted%20image%2020261119191454.png)
![Pasted image 20261119191508.png\|429](/img/user/accessory/Pasted%20image%2020261119191508.png)
-> 一个只有一个隐藏层的感知机可以计算任意布尔函数！
问题在于在这一个隐藏中，所需要的神经元的数量和子句的数量是一样的。所以问题的核心在于子句的数量。
为了能够更好的理解合并，引入了Karnaugh maps(卡诺图)
![Pasted image 20261119192121.png\|213](/img/user/accessory/Pasted%20image%2020261119192121.png)
Karnaugh maps是一种输入的拓扑表示，每个格子对应一种输入组合，格子中的值或者颜色表示输出，横向或者纵向相邻的格子仅有一个输入变量不同，左右边界和上下边界也视为相邻
那么在化简时，如图上第一列的输出都是1，这告诉我们WX的值无关紧要只要确保yz是00就能触发函数即$\bar{Y}\bar Z$ . 绿色的圈表示和Z无关WXY只要是010就好 ...
这样就能写出来一个化简过的式子 并构建一层隐含层的MLP
![Pasted image 20261119192739.png\|452](/img/user/accessory/Pasted%20image%2020261119192739.png)
最坏的情况：其实就是没法分组的情况，即交错分布棋盘模式
![Pasted image 20261119193012.png\|203](/img/user/accessory/Pasted%20image%2020261119193012.png)
在这个例子中，构建一个只有一个隐藏层的多层感知机，需要8个神经元
如果是三维的：
![Pasted image 20261119193900.png\|403](/img/user/accessory/Pasted%20image%2020261119193900.png)
-> 如果构建一个隐藏层的多层感知机，N个变量最坏的情况需要$2^{N-1}$个神经元。也就是说神经元的数量将会是n的指数级
实际上例子中的这个函数的特点是：任何时候一位改变，输出就会改变。所以如果允许有深度，那我们完全可以用异或来表示这个函数
![Pasted image 20261119212914.png\|379](/img/user/accessory/Pasted%20image%2020261119212914.png)
这个图或者说这棵树 有点像赫夫曼树 需要3(N-1)个神经元

-> 如果允许有深度，所需要的神经元数量可以呈指数下降：深度的重要之处 -- 所以一个只有一个线性隐藏层的需要指数级数量单元的网络 当允许深度的存在 会变成线性数量的单元！

深度的限制是什么？
首先要知道 异或是组合性的，所以我们完全可以采用完全二叉树这样的方式来组合
![Pasted image 20261119213824.png](/img/user/accessory/Pasted%20image%2020261119213824.png)
如果我们采用这样的方式，需要$2log_2N$层

那如果只允许有7层呢？ -- 可以先像上面那样组织六层 最后一层采用只有一层隐藏层的做法
也就是说如果限制了层数或者说深度，所需要的单元数量也是输入大小的指数级别

我们会倾向使用更少的神经元 因为每一个神经元都会有计算的时间；我们更倾向于使用窄而深的MLP，深度做的是计算的顺序化
### MLPs as universal classifiers

