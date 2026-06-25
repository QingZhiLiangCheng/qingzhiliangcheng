---
{"created":"2025-12-01T11:12","updated":"2026-06-01T19:56","dg-publish":true,"permalink":"/math/考研线性代数/Topic 7 行列式/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2025-12-01T11:12","updated":"2026-06-01T19:56"}}
---

### Overview
行列式是针对于方阵说的
方阵有两大话题：行列式和特征值
需要行列式的原因：求特征值
行列式 是一个 与方阵有关的数，这一个数可以告诉我们矩阵什么样子，比如行列式告诉了我们矩阵的可逆性
行列式的表示，det A 或者$|A|$

### 行列式的三个基础性质
教授首先介绍了行列式的三个基本性质，这三个性质定义了行列式是什么
**性质1: 对于单位矩阵I，有|I|=1.**
**性质2:  交换行，行列式的值的符号会相反**
从性质一和性质二实际上就能得到所有的置换矩阵的行列式，值为1或-1
**性质3.a: 行列式按行提取矩阵中的系数**
$\begin{vmatrix}ta&tb\\c&d\end{vmatrix}=t\begin{vmatrix}a&b\\c&d\end{vmatrix}$
-> $|tA|=t^nA$
**性质3.b: 行列式是一个线性函数，但是线性反映在每一行上**
${\begin{vmatrix}a+a’& b+b’\\c& d\end{vmatrix}=\begin{vmatrix}a& b\\c& d\end{vmatrix}+\begin{vmatrix}a’& b’\\c& d\end{vmatrix}}$
其实有了性质3，我感觉就能构造出所有的矩阵的行列式?

### 推导出的行列式性质
行列式的其他性质都可以由上面的三条基本性质推导出来
**性质4: 如果两行相等，那么行列式等于0**
可以用行列式2证明，交换相等的两行，一方面矩阵完全不变，一方面行列式会取反，所以这个数只能是0
**性质5: 如果方阵的两行成比例，那么其行列式值也为0**
性质4和性质3得到
**性质6: 从k行中减去第i行的l倍，行列式不变**
$$
\begin{vmatrix}a& b\\c-la& d-lb\end{vmatrix}\stackrel{3.b}{=}\begin{vmatrix}a& b\\c& d\end{vmatrix}+\begin{vmatrix}a& b\\ -la& -lb\end{vmatrix}\stackrel{3.a}{=}\begin{vmatrix}a& b\\c& d\end{vmatrix}-l\begin{vmatrix}a& b\\a& b\end{vmatrix}\stackrel{4}{=}\begin{vmatrix}a& b\\c& d\end{vmatrix}
$$
这个动作实际上是我们在做消元中做的基础行变换的动作，这个性质也就是说，做基础行变换实际上并不会改变矩阵的行列式
一般用性质6有两个作用
- 像消元法中的作用，把某个或某些元素变为0
- 加一下如果有公因数，把公因数提出去

**性质7: 如果方阵的某一行全为0，那么其行列式值也为0**
$\begin{vmatrix}0&0\\c&d\end{vmatrix}=\begin{vmatrix}t\cdot0&t\cdot0\\c&d\end{vmatrix}=t\begin{vmatrix}0&0\\c&d\end{vmatrix}$ 
这说明确实可以用行列式才判断是否可逆，这是我们消元完可能出现的情况，即一行全为0，而它的行列式为0，也就是说，行列式实际上可以与秩，可逆挂钩


**性质8: 上三角矩阵的对应行列式的值等于其对角线上元素的乘积**
如果有上三角矩阵U，其行列式为$U=\left|\begin{array}{cccc}{d_{1}} & {*} & {\cdots} & {*} \\ {0} & {d_{2}} & {\cdots} & {*} \\ {\vdots} & {\vdots} & {\ddots} & {\vdots} \\ {0} & {0} & {\cdots} & {d_{n}}\end{array}\right|$,则$|U|=d_1d_2\cdot d_n$
证明1：用性质5将最后一行开始，将对角元素上方的 * 元素依次变为0，$D=\begin{vmatrix}d_{1}& 0& \cdots& 0\\0& d_{2}& \cdots& 0\\\vdots& \vdots& \ddots& \vdots\\0& 0&\cdots&d_{n}\end{vmatrix}$的对角行矩阵，在使用性质3得到$d_nd_{n-1}\cdots d_1\begin{vmatrix}1& 0& \cdots& 0\\0& 1& \cdots& 0\\\vdots& \vdots& \ddots& \vdots\\0& 0& \cdots& 1\end{vmatrix}$,然后使用性质1

从矩阵A到U的过程中，可能会出现换行，矩阵A的真正的行列式可能不与U的行列式同号，这是由性质2决定的

证明2：从最后一行网上选数 只有一种不为0的选法 后面会再提到

**性质9: |AB| = |A| |B|**
当A是奇异矩阵（不可逆）时，|A|=0, A不可逆，乘积AB也一定不可逆(相当于对A中的列向量做了某种线性组合，A的列空间不变），|AB|=0, |A||B|=0
当A是非奇异矩阵是，可以利用到性质5，任何一个可逆矩阵A都能通过一系列初等行变换转换为单位矩阵I，$A=E_kE_{k-1}...E_1I$，根据前面的性质 ？可知|AB|=|A| |B|
行列式并不具备线性性质，也不具备加法性质，但是具备乘法性质
通过性质9，实际上能推出**逆矩阵的行列式公式**
$AA^{-1}=I \to |AA^{-1}|=1 \to |A||A^{-1}|=1 \to |A^{-1}|=\frac{1}{|A|}$
也可以推出$|A^2| = |A|^2$
但是要注意 $|2A| = 2^n|A|$，2A实际上是把A中的每一个元素都乘以2，一行一行往外提，需要提n行

**性质10: $|A|^T = |A|$**
前面一直在关注行的属性给行列式带来的变化，有了这条性质，行的属性同样适用于列 -- 行和列是等效的
证明，将矩阵化为LU形式有$A^T=U^TL^T,A=LU$,那么$|A^T|=|U^TL^T|=|U^T||L^T|, |A|=|LU|=|L||U|$ L和U都是三角啊，都是对角线

---
### 和特定矩阵的一些联系的性质

**性质11: 当且仅当矩阵A为奇异矩阵或者不可逆时，$|A|=0$, 当且仅当A可逆时， $|A|≠0$**
首先由性质8可以知道，如果A为非奇异矩阵或者可逆的时候，在化成行阶梯形矩阵的时候，每一列都存在主元，所以对角线中实际上不会存在0元素
其实也可以记逆矩阵公式 分母是个A的行列式
补充：
这是个充要条件，[[math/考研线性代数/Topic 4 逆矩阵 转置 对称 初等矩阵 置换 行阶梯 行最简 分块矩阵\|Topic 4 逆矩阵 转置 对称 初等矩阵 置换 行阶梯 行最简 分块矩阵]]中给出了证明
必要性：
若 $A$ 可逆，则存在 $A^{-1}$ 使得 $AA^{-1} = E$。
对等式两边取行列式：
$$|AA^{-1}| = |E|$$
根据行列式乘法性质 $|AB| = |A||B|$：
$$|A| \cdot |A^{-1}| = 1$$
由于两个数的乘积为 1，则 $|A|$ 必不为 0（即 $|A| \neq 0$）。
充分性：
若 $|A| \neq 0$，我们要证明 $A$ 可逆。
根据伴随矩阵的性质，我们有：
$$A \cdot A^* = A^* \cdot A = |A|E$$
由于已知 $|A| \neq 0$，等式两边同时除以 $|A|$：
$$A \cdot \left( \frac{1}{|A|} A^* \right) = \left( \frac{1}{|A|} A^* \right) \cdot A = E$$
根据逆矩阵的定义，如果存在一个矩阵 $B$ 使得 $AB = BA = E$，则 $A$ 可逆且 $B$ 是 $A$ 的逆。
显而易见，这里的 $B = \frac{1}{|A|} A^*$。


**性质12：若A是n阶矩阵，$A^*$是A的伴随矩阵，则$|A^*|=|A|^{n-1}$**
因为在伴随矩阵里面我们学过一个重要公式$AA^*=|A|E$, 两边同时取行列式，由性质10得到$|AA^*|=|A||A^*|=||A|E|$, 由性质3得到，$|A||A^*|=|A|^{n}|E|$,约掉一个|A|，得到$|A^*|=|A|^{n-1}$

**性质13：若A是n阶可逆矩阵，$A^{-1}$是A的逆矩阵，则$|A^{-1}|=|A|^{-1}$** 
这个很好得到，由$A^{-1}A=E$就可以很简单的得到

**性质14：若A和B相似，则|A|=|B|**
相似的定义$P^{-1}AP=B$, 取行列式得$|P^{-1}AP|=|B|$,由性质10得到$|P^{-1}||A||P|=|B|$,而由性质13得知$|P|和|P^{-1}$互为倒数，所以得到|A|=|B|

### 行列式：超体积
行列式的几何意义是向量所张成物体的一种超体积的测度
对于一个 2x2 矩阵，行列式是两个向量张成的平行四边形的**面积**
对于一个 3x3 矩阵，行列式是三个向量张成的平行六面体的**体积**

行列式的几何意义实际上是一种测度，是向量所围成的那个东西的测度
对于二阶行列式来说就是平行四边形的体积，对于三阶行列式来说就是平行六面体的体积...
![Pasted image 20250929162432.png\|500](/img/user/accessory/Pasted%20image%2020250929162432.png)
事实上，知道了几何意义，对于行列式的性质就有了一个集合的理解
- 交换两列，向量还是那些向量
- 行倍数
- 行加减
- 两行相同 行列式为0

### 行列式的公式求解
#### 行列式公式
行列式的一个背景是解方程组 一个背景是超体积的测度
对于一个方程组
$$\begin{cases} a_1x + b_1y = c_1 \\ a_2x + b_2y = c_2 \end{cases}$$
通过代数消元（例如：第一个方程乘 $b_2$，第二个方程乘 $b_1$ 后相减），可以得到关于 $x$ 和 $y$ 的表达式：
$$(a_1b_2 - a_2b_1)x = c_1b_2 - c_2b_1$$
$$(a_2b_1 - a_1b_2)y = c_1a_2 - c_2a_1$$
有一个东西是回避不掉的，即两个数相乘的差，所以引入了行列式
$$\begin{vmatrix} a_1 & b_1 \\ a_2 & b_2 \end{vmatrix} = a_1b_2 - a_2b_1$$
三阶行列式同理...

**二阶行列式公式**
$\left|\begin{array}{ll}{a} & {b} \\ {c} & {d}\end{array}\right|=a d-b c$
通过行列式的性质，我们就能够推到出这个公式来了
$$
\displaylines{
\begin{vmatrix}a& b\\c& d\end{vmatrix}\stackrel{3.b}{=}\begin{vmatrix}a& 0\\c& d\end{vmatrix}+\begin{vmatrix}0& b\\c& d\end{vmatrix}\stackrel{3.b}{=}\begin{vmatrix}a& 0\\c& 0\end{vmatrix}+\begin{vmatrix}a& 0\\0& d\end{vmatrix}+\begin{vmatrix}0& b\\c& 0\end{vmatrix}+\begin{vmatrix}0& b\\0& d\end{vmatrix}\\
其中\begin{vmatrix}a& 0\\c& 0\end{vmatrix}\stackrel{6, 10}{=}0,\quad\begin{vmatrix}a& 0\\0& d\end{vmatrix}\stackrel{7}{=}ad,\quad \begin{vmatrix}0& b\\c& 0\end{vmatrix}\stackrel{2,7}{=}-bc,\quad \begin{vmatrix}0& b\\0& d\end{vmatrix}\stackrel{6,10}{=}0\\
综上，\begin{vmatrix}a& b\\c& d\end{vmatrix}=ad-bc
}
$$
其实这是给出了一个推到所有行列式公式的方法 -- 分解行列式
而分解后的行列式实际上是存在一些列为零的行列式的情况，不为零的行列式正好是一列一个元素(或者是一行一个元素)的排列组合
所以其实很容易能推出三阶行列式的公式
**三阶行列式公式**
$$
\displaylines{
\begin{align}
\begin{vmatrix}a_{11}& a_{12}& a_{13}\\a_{21}& a_{22}& a_{23}\\a_{31}& a_{32}& a_{33}\end{vmatrix}&=\begin{vmatrix}a_{11}& 0& 0\\0& a_{22}& 0\\0& 0& a_{33}\end{vmatrix}+\begin{vmatrix}a_{11}& 0& 0\\0& 0& a_{23}\\0& a_{32}& 0\end{vmatrix}+\begin{vmatrix}0& a_{12}& 0\\a_{21}& 0& 0\\0& 0& a_{33}\end{vmatrix}\\&+\begin{vmatrix}0& a_{12}& 0\\0& 0& a_{23}\\a_{31}& 0& 0\end{vmatrix}
+\begin{vmatrix}0& 0& a_{13}\\a_{21}& 0& 0\\0& a_{32}& 0\end{vmatrix}+\begin{vmatrix}0& 0& a_{13}\\0& a_{22}& 0\\a_{31}& 0& 0\end{vmatrix}\\
&=a_{11}a_{22}a_{33}-a_{11}a_{23}a_{32}-a_{12}a_{21}a_{33}+a_{12}a_{23}a_{31}+a_{13}a_{21}a_{32}-a_{13}a_{22}a_{31}
\end{align}
}
$$
首先这里说明一下，这其实就是我们在一开始学的那个叉叉子的公式，其实这只是推导出来公式后发现的一个规律，这个规律只适用于三阶行列式
能看到其实我们是先确定第一行a11的位置，然后在做其他列其他行的元素的排列组合
换句话说，第一行实际上有n个选择，选择之后就确定了一列，第二行只能有n-1个选择了... 这其实能够得到一般公式
**Big Formula**
$$
\displaylines{
|A|=\sum_{n!} \pm a_{1\alpha}a_{2\beta}a_{3\gamma}\cdots a_{n\omega}, \quad(\alpha, \beta, \gamma, \cdots,\omega)=P_n^n
}
$$

example
$$
\displaylines{
\begin{vmatrix}0& 0& \color{red}1& \color{blue} 1\\0& \color{red} 1& \color{blue} 1& 0\\ \color{red} 1& \color{blue} 1& 0& 0\\\color{blue} 1& 0& 0&  \color{red} 1\end{vmatrix}
}
$$
其实在这个行列式中α只能为3或4，如果选定α为3，实际上只有红色的这一种组合，同样的如果选定α等于4，只有蓝色这一种组合 -- 其余都是0行列式
- 对于第一组非零行列式(红色部分)，其对应的排列是(3,2,1,4),变成(1,2,3,4)只需要一步操作，所以符号为-
- 对于第二组非零行列式(蓝色部分)，其对应的排列是(4,3,2,1),变成(1,2,3,4)需要两步从操作，符号为+

通过统计当前排列变换到顺序排列(1,2,3, ... ,n)的操作数可以确定非零行列式的符号，但这种方法显然不实用，难以编程实现且当仅适用于行列式的阶数较小时
一般用逆序数来判断非零行列式的正负号。逆序数就是从左到右遍历当前排列中的每一个数，统计右侧有几个数比自己小，然后求和
逆序数为偶数，称排列为偶排列，非零行列式取+
逆序数为奇数，称排列为奇排列，非零行列式取-
其中的道理在于奇排列做一次交换后即为偶排列，偶排列做一次交换后即为奇排列，初始顺序排列(1,2,3, ... ,n)为偶排列

#### 代数余子式
对于上面的3 by 3的矩阵，我们已经得到了行列式公式
$$
\displaylines{
原式=a_{11}a_{22}a_{33}-a_{11}a_{23}a_{32}-a_{12}a_{21}a_{33}+a_{12}a_{23}a_{31}+a_{13}a_{21}a_{32}-a_{13}a_{22}a_{31}
}
$$
可以改成
$$
\displaylines{
a_{11}(a_{22}a_{33}-a_{23}a_{32})+a_{12}(-a_{21}a_{33}+a_{23}a_{31})+a_{13}(a_{21}a_{32}-a_{22}a_{31})\\
=\begin{vmatrix}a_{11}& 0& 0\\0& a_{22}& a_{23}\\0& a_{32}& a_{33}\end{vmatrix}+\begin{vmatrix}0& a_{12}& 0\\a_{21}& 0& a_{23}\\a_{31}& 0& a_{33}\end{vmatrix}+\begin{vmatrix}0& 0& a_{13}\\a_{21}& a_{22}& 0\\a_{31}& a_{32}& 0\end{vmatrix}\\
=a_{11}(\begin{vmatrix} a_{22}& a_{23}\\a_{32}& a_{33}\end{vmatrix})+a_{12}(-\begin{vmatrix} a_{21}& a_{23}\\a_{31}& a_{33}\end{vmatrix})+a_{13}(\begin{vmatrix} a_{21}& a_{22}\\a_{31}& a_{32}\end{vmatrix})
}
$$

其实还是一旦a11选定，剩余因子就只能从剩余的n-1行和n-1列中提取，而剩余的因子正好组成一个行列式, 而这个就叫做代数余子式
改写中括号的部分就是代数余子式，比如$(-a_{21}a_{33}+a_{23}a_{31})$是$a_{12}$的代数余子式
代数余子式(cofactors)是与选定元素配套的，这也是'co-'的意思
而仅仅是剩下的元素按原相对顺序组成的 $(n-1)$ 阶行列式 叫 余子式！
选定元素$a_{ij}$的代数余子式为：将原行列式中第i行和第j列抹去后得到的n-1阶行列式，再取正负(i+j偶数为+，奇数为-)
$$
\displaylines{
C_{ij}=(-1)^{i+j}\cdot \det(去掉 i 行和j列的一个n-1矩阵)
}
$$
所以根据代数余子式，我们能得到一个新的求解行列式的方法
一直按行展开！而且选行的时候是有一定技巧
降阶了！

#### 一些关键的行列式
**三角行列式**
结论：上(下)三角行列式的值等于主对角线元素的乘积.
$$D = \begin{vmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ 0 & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & a_{nn} \end{vmatrix} =D = \begin{vmatrix} a_{11} & 0 & \cdots & 0 \\ a_{21} & a_{22} & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ a_{n1} & a_{n2} & \cdots & a_{nn} \end{vmatrix} = \prod_{i=1}^{n} a_{ii} = a_{11} a_{22} \cdots a_{nn}= a_{11} a_{22} \cdots a_{nn}$$
前面我们在性质8中介绍了上三角矩阵的对应行列式的值等于其对角线上元素的乘积 是通过化为I的角度来说的 下三角依然相同
另一种理解的角度是：从最后一行往上选数，最后一行为了让最后的项非零，只能选$a_{nn}$，倒数第二行只能选$a_{n(n-1)}$...

**副对角线三角行列式**
$$D = \begin{vmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & \cdots & a_{2,n-1} & 0 \\ \vdots & \ddots & \ddots & \vdots \\ a_{n1} & 0 & \cdots & 0 \end{vmatrix} = \begin{vmatrix}
0 & \cdots & 0 & a_{1n} \\
0 & \cdots & a_{2,n-1} & a_{2n} \\
\vdots & \ddots & \ddots & \vdots \\
a_{n1} & a_{n2} & \cdots & a_{nn}
\end{vmatrix}=(-1)^{\frac{n(n-1)}{2}} a_{1n} a_{2,n-1} \cdots a_{n1}$$

不管是从选数逆序数的角度，还是从交换列回到主对角线三角行列式的方法（实际上逆序数的本质就是交换列）都能得到这个公式

**爪型行列式**
![Pasted image 20260509161314.png\|400](/img/user/accessory/Pasted%20image%2020260509161314.png)
分正反两种，正向的是把每一行都加在第一行或者把每一列都加在第一列；反向的是把每一行加在最后一行或者把每一列加在最后一列
本子上的 eg9, 补充eg6, eg10, 补充eg11 都能用爪型来做
但是很多时候考察爪型都是隐晦的爪型 比如eg9, eg10, 补充eg11 典型的特征是盖住对角线，每一列的元素的数值是相同的，这个时候需要用一定的变换比如把第一行的-1倍加到各行 变成一个标准的爪型行列式

**斜型行列式**
![Pasted image 20260509161815.png\|300](/img/user/accessory/Pasted%20image%2020260509161815.png)
两种操作手法
逐行相加：逐行相加的意思是第一行加到第二行，然后再把新的第二行加到第三行....
or
每行都加到第一行

补充eg7

**拉普拉斯行列式**
$$D = \begin{vmatrix} \mathbf{A} & \mathbf{O} \\ \mathbf{C} & \mathbf{B} \end{vmatrix} = \begin{vmatrix} \mathbf{A} & \mathbf{B} \\ \mathbf{O} & \mathbf{C} \end{vmatrix} = |\mathbf{A}| |\mathbf{C}|$$
$$D = \begin{vmatrix} \mathbf{O} & \mathbf{A} \\ \mathbf{C} & \mathbf{O} \end{vmatrix} = (-1)^{m \times n} |\mathbf{A}| |\mathbf{C}|$$

**ABBA型行列式**
$$D = \begin{vmatrix} \mathbf{A} & \mathbf{B} \\ \mathbf{B} & \mathbf{A} \end{vmatrix}=||A+B||A-B||=|A^2-B^2|$$
推导：
$$D = \begin{vmatrix} \mathbf{A} + \mathbf{B} & \mathbf{B} \\ \mathbf{B} + \mathbf{A} & \mathbf{A} \end{vmatrix}== \begin{vmatrix} \mathbf{A} + \mathbf{B} & \mathbf{B} \\ \mathbf{O} & \mathbf{A} - \mathbf{B} \end{vmatrix}$$
然后用拉普拉斯就可以了

**范德蒙德行列式**
$$V_n = \begin{vmatrix} 1 & 1 & \cdots & 1 \\ x_1 & x_2 & \cdots & x_n \\ x_1^2 & x_2^2 & \cdots & x_n^2 \\ \vdots & \vdots & \ddots & \vdots \\ x_1^{n-1} & x_2^{n-1} & \cdots & x_n^{n-1} \end{vmatrix}= \prod_{1 \le i < j \le n} (x_j - x_i)$$
一个简单的记住的方法就是看第二行，高年级欺负低年级欺负遍，$(x_n-x_{n-1})(x_N-x_{n-1})...(x_n-x_1)$ 然后$x_{n-1}$再开始欺负



### 逆矩阵公式
之前学过高斯若当法 就是通过$AI \to IA^{-1}$
今天所讲的是一种行列式的方法
$$
\displaylines{
A^{-1}=\frac{1}{|A|}C^T
}
$$
假设矩阵为 $A$，我们将 $A$ 中每一个元素 $a_{ij}$ 都替换成它对应的**代数余子式** $C_{ij}$，得到的新矩阵就叫**代数余子式矩阵** $C$
其中$C^T$是由代数余子式组成的，我们将 $A$ 中每一个元素 $a_{ij}$ 都替换成它对应的代数余子式 $C_{ij}$，得到的新矩阵就叫代数余子式矩阵 $C$，$C^T$是代数余子式矩阵的转置，这个矩阵就是之前在课本上学的伴随矩阵啊，当时学的公式是
$$A^{-1}=\frac{A^*}{|A|}$$
要想证明这个式子成立，实际上是证明$AA^{-1}=I$即
$$
AC^T=|A|I
$$
$$
\displaylines{
\begin{bmatrix}a_{11}& a_{12}& \cdots& a_{1n}\\\vdots& \vdots& \ddots& \vdots\\a_{n1}& a_{n2}& \cdots& a_{nn}\end{bmatrix}\begin{bmatrix}C_{11}& \cdots& C_{n1}\\C_{12}& \cdots& C_{n2}\\\vdots& \ddots& \vdots\\C_{1n}& \cdots& C_{nn}\end{bmatrix}=\overbrace{\begin{bmatrix}|A|&0&\cdots&0\\0&|A|&\cdots&0\\\vdots&\vdots &\ddots&\vdots\\0&0&\cdots&|A|\end{bmatrix}}^{D}
}
$$

$AC^T$计算结果的第一个元素的结果实际上就是A的第一行和$C^T$的第一列的点乘，即$a_{11} * C_{11}+a_{12}*C_{12}+...+a_{1n}*C_{1n}$ , 关键是这个式子不正是A矩阵求行列式的时候的按行展开吗，所以说计算结果的第一个矩阵的值为|A|
同理，只要是对角线上的值都是|A|
而其他地方的值都是0，这是因为比如$a_{21} * C_{11}+a_{22}*C_{12}+...+a_{2n}*C_{1n}$如果按照按行展开的样子来看的话，实际上他的原行列式应该为类似这样子
$$
a_{21}(\begin{vmatrix} a_{22}& a_{23}\\a_{32}& a_{33}\end{vmatrix})+a_{22}(-\begin{vmatrix} a_{21}& a_{23}\\a_{31}& a_{33}\end{vmatrix})+a_{23}(\begin{vmatrix} a_{21}& a_{22}\\a_{31}& a_{32}\end{vmatrix})=
\begin{vmatrix}a_{21}&a_{22}&a_{23}\\a_{21}&a_{22}&a_{23}\\a_{31}&a_{32}&a_{33}\\\end{vmatrix}
$$
换句话说，他们的前两行是相同的，所以说行列式为0
有了逆矩阵公式，我们能够清楚地看到，当改变原矩阵中的一个元素时，会给逆矩阵带来什么样的变化！

### 伴随矩阵
伴随矩阵的一个重要的公式：
$$
AA^*=A^*A=|A|E
$$
推导：
$$
\begin{align}
&AA^*= \begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{n1} & a_{n2} & \cdots & a_{nn} \end{pmatrix}\begin{pmatrix} A_{11} & A_{21} & \cdots & A_{n1} \\ A_{12} & A_{22} & \cdots & A_{n2} \\ \vdots & \vdots & \ddots & \vdots \\ A_{1n} & A_{2n} & \cdots & A_{nn} \end{pmatrix}\\
\end{align}
$$
一行乘一列 $c_{11} = a_{11}A_{11} + a_{12}A_{12} + \cdots + a_{1n}A_{1n}=|A|$， $c_{12} = a_{11}A_{21} + a_{12}A_{22} + \cdots + a_{1n}A_{2n}$中是相当于把A的行列式第二列换成A的第一列=0
所以
$$=\begin{pmatrix} |A| & 0 & \cdots & 0 \\ 0 & |A| & \cdots & 0 \\ \vdots & \vdots & \ddots & \vdots \\ 0 & 0 & \cdots & |A| \end{pmatrix}=|A|E$$

由上面的结论$AA^*=A^*A=|A|E$,可以推出下面这些结论

---
如果我把$|A|$看做一个数，除到$A^*$下面，即$A\frac{A^*}{|A|}=\frac{A^*}{|A|}A=E$
如果把A看做A, $\frac{A^*}{|A|}$看做B -- 满足了可逆的定义，立即推
$$
A^{-1}=\frac{A^*}{|A|}
$$
这就是逆矩阵公式，一种求逆矩阵的方式

---
如果我把$|A|$看做一个数，除到$A$下面，即$\frac{A}{|A|}A^*=\frac{A}{|A|}A^*=E$
如果把$\frac{A}{|A|}$看做A，把$A^*$看做B -- 满足了可逆的定义，立即推
$$
(A^*)^{-1}=\frac{A}{|A|}
$$

---
A是个任意的矩阵，那其实可以把A换成$A^{-1}$,即$A^{-1}(A^{-1})^*=(A^{-1})^*A^{-1}=|A^{-1}|E$
我们在两边同时左乘矩阵 $A$：
$$A \cdot A^{-1}(A^{-1})^* = A \cdot \frac{1}{|A|}E$$
$$E(A^{-1})^* = \frac{A}{|A|}$$

---
立即能得到一个重要的结论
$$(A^*)^{-1} = (A^{-1})^*$$
 -- 伴随的你=你的伴随

---
$$(kA)^* = k^{n-1} A^*$$
简单推导如下
我们设$B=kA$ $B_{ij}$ 代表矩阵 $B = kA$ 的代数余子式
$$B_{ij}=(-1)^{i+j} k^{n-1} \begin{vmatrix} a_{11} & \cdots & a_{1n} \\ \vdots & \ddots & \vdots \\ a_{n1} & \cdots & a_{nn} \end{vmatrix} \text{ (此处为 n-1 阶)}=k^{n-1} A_{ij}$$
$$(kA)^* = \begin{pmatrix} B_{11} & B_{21} & \cdots & B_{n1} \\ B_{12} & B_{22} & \cdots & B_{n2} \\ \vdots & \vdots & \ddots & \vdots \\ B_{1n} & B_{2n} & \cdots & B_{nn} \end{pmatrix}$$
$$(kA)^* = \begin{pmatrix} k^{n-1}A_{11} & k^{n-1}A_{21} & \cdots & k^{n-1}A_{n1} \\ k^{n-1}A_{12} & k^{n-1}A_{22} & \cdots & k^{n-1}A_{n2} \\ \vdots & \vdots & \ddots & \vdots \\ k^{n-1}A_{1n} & k^{n-1}A_{2n} & \cdots & k^{n-1}A_{nn} \end{pmatrix}$$
$$(kA)^* = k^{n-1} \begin{pmatrix} A_{11} & A_{21} & \cdots & A_{n1} \\ A_{12} & A_{22} & \cdots & A_{n2} \\ \vdots & \vdots & \ddots & \vdots \\ A_{1n} & A_{2n} & \cdots & A_{nn} \end{pmatrix}$$
$$(kA)^* = k^{n-1} A^*$$

---
二阶矩阵伴随矩阵的规律
![Pasted image 20260513170158.png\|300](/img/user/accessory/Pasted%20image%2020260513170158.png)
主对角线互换 副对角线变相反数

---
$$
(A^*)^T=(A^T)^*
$$
这个公式比较好理解

---
还是由最初的公式$AA^*=|A|E$，如果两边同时取行列式得$|AA^*|=||A|E|$, 其中$|AA^*|=|A||A^*|, ||A|E|=|A|^{n}$,所以有$|A||A^*|=|A|^n$,所以有
$$
|A^*|=|A|^{n-1}
$$

---
如果将A* 看做A来背公式$AA^*=|A|E$,得到$A^*(A^*)^*=|A^*|E$, 得到$(A^*)^*=|A^*|(A^*)^{-1}$，其中$|A^*|=|A|^{n-1}, (A^*)^{-1}=\frac{A}{|A|}$, 得到$(A^*)^*=|A|^{n-1}\frac{A}{|A|}$,得到
$$
(A^*)^*=|A|^{n-2}A
$$

---
$$r(A^*) = \begin{cases} n, & r(A) = n \\ 1, & r(A) = n-1 \\ 0, & r(A) < n-1 \end{cases}$$

r(A)=n时，A可逆，$|A|≠0$，也就是会说$A^*=|A|A^{-1}$, 而$A^{-1}$的秩跟A的秩是一样的，前面再乘以一个常数 秩也是一样的

r(A)=n-1时 有两种考虑的办法
一种是我们可以想象成A中有两列是相同的，不防就设为前两列，所以再构成代数余子式的时候，只要删除的不是第一列或者第二列，构成的行列式中就存在两列相同，行列式为0；而如果删除的是第一列或者第二列，他们所构成的余子式还是相同的 -- 所以最后的$r(A^*)$其实为1
第二种思考方式是看$AA^*=|A|E$, r(A)=n-1 意味着$|A|=0$, 所以$A A^* = |A|E = O$，所以$A^*$其实是Ax=0的零空间，而A的秩为n-1 只存在一个自由变量，所以零空间为1维

r(A)<n-1时也很明显，任意删掉一行一列，总能剩下存在有相同的列，所以代数余子式通通为0



### 克拉默法则
有了逆矩阵公式后，对Ax=b现在就有
$$
\displaylines{
x=A^{-1}b=\frac{1}{|A|}C^Tb
}
$$
更进一步，如果我们思考$C^T$b是什么的话，对于$C^Tb$结果中的每一个元素，都是一行点乘一列的结果，而这每一个一行点乘一列就是对于行列式按列展开的一个结果
所以有了克拉默法则
$$x_i = \frac{|B_i|}{|A|}$$
$$
\displaylines{
B_1=\begin{bmatrix}b_1& a_{12}& \cdots& a_{1n}\\b_2& a_{22}& \cdots& a_{2n}\\\vdots& \vdots& \ddots& \vdots\\b_n& a_{n2}& \cdots& a_{nn}\end{bmatrix}
}
$$
实际上就是换掉对应的列
克拉默法则很漂亮，但并不实用hhhh，计算量太大了
克拉默法则的完整描述是：

设有如下包含 $n$ 个未知数 $x_1, x_2, \dots, x_n$ 的线性方程组：$$\begin{cases}
a_{11}x_1 + a_{12}x_2 + \cdots + a_{1n}x_n = b_1 \\
a_{21}x_1 + a_{22}x_2 + \cdots + a_{2n}x_n = b_2 \\
\vdots \\
a_{n1}x_1 + a_{n2}x_2 + \cdots + a_{nn}x_n = b_n
\end{cases}$$记其系数行列式为 $|A|$：$$|A| = \begin{vmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{n1} & a_{n2} & \cdots & a_{nn} \end{vmatrix}$$如果 $|A| \neq 0$，则方程组**有唯一解**。第 $j$ 个未知数 $x_j$ 的解可以通过下式求得：$$x_i = \frac{|A_i|}{|A|} \quad (i = 1, 2, \dots, n)$$其中，$|A_i|$ 是将行列式 $|A|$ 中的第 $i$ 列替换为常数项列 $\mathbf{b} = [b_1, b_2, \dots, b_n]^T$ 后得到的行列式：$$D_j = \begin{vmatrix} a_{11} & \cdots & b_1 & \cdots & a_{1n} \\ a_{21} & \cdots & b_2 & \cdots & a_{2n} \\ \vdots & & \vdots & & \vdots \\ a_{n1} & \cdots & b_n & \cdots & a_{nn} \end{vmatrix}$$
注意到分母的$|A|≠0$，所以有一个推论是，$|A|≠0$，n个方程n个未知数有唯一解 也就是0解
其实我觉得从这个角度更好理解：由于是n行n列的行列式，如果$|A|≠0$，说明满秩啊！没有自由变量 -> Ax=0只有一个0向量解 ； 而由于是n维满秩 所以章程了整个n维空间 那b一定能落在这个列空间中呐！ 能找出一个线性组合来 所以有唯一解 