---
{"created":"2025-12-01T11:10","updated":"2026-05-29T21:36","dg-publish":true,"permalink":"/math/考研线性代数/Topic 6 正交和投影/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2025-12-01T11:10","updated":"2026-05-29T21:36"}}
---

### 向量正交
什么是两个向量正交？
正交是垂直的另一种说法，意味着在n维空间中，向量的夹角为90度
如果两个向量正交，垂直意味着点积为0，如果用矩阵乘法来表示的话，就是$x^Ty=0$
![Pasted image 20250915163113.png\|200](/img/user/accessory/Pasted%20image%2020250915163113.png)

下面来证明这个结论
勾股定理有${|x|}^2+{|y|}^2={|x+y|}^2$, 怎么理解这个$|x|^2$? 实际上就是x和自己的点积，就是$x^Tx$
那么这个式子就变成了$x^Tx+y^Ty=(x+y)^T(x+y)$ , 等号右侧转置符号进去得$(x^T+y^T)(x+y)$, 展开得$x^Tx+x^Ty+y^Tx+y^Ty$（矩阵的运算律)， 化简得 $0=x^Ty+y^Tx$
由于$x^Ty=y^Tx$ (事实上都是$x_i$和$y_i$的乘积和)，所以得到了$x^Ty=0$
另外多一嘴，如果x和y中其中一个为零向量，那么任意另一个向量可以为任何值，也就是说，零向量和任何向量都正交

### 空间正交
什么叫空间的正交？子空间S和子空间T正交是什么意思？
子空间S和子空间T正交意味着every vectors in S is orthogonal to every vectors in T.
这里注意一个特别容易混淆的情况，事实上xoy面和yoz面不是正交的，如果xoy面取两个面的交界线(也就是x轴)，yoz面选择一条和交界线平行的向量，那么这两个向量就不垂直，而是平行的
这也说明，如果两个子空间正交，一定不会交于某个非零向量

### 矩阵的四个子空间的正交
![Pasted image 20250915164810.png\|500](/img/user/accessory/Pasted%20image%2020250915164810.png)
这张图是我们之前讨论过的四种子空间以及他们的维数，除此之外，这张图还展示了零空间和行空间是正交的，列空间和左零空间是正交的
这里以row space is orthogonal to the null space为例来看一下
对于Ax=0，事实上给出了行空间和零空间的所有信息
$$
Ax=\begin{bmatrix}row_1 \space of \space A\\row_2 \space of \space A\\...\\row_m \space of \space A\end{bmatrix}
\begin{bmatrix}x_1\\...\\x_n\end{bmatrix}=
\begin{bmatrix}row_1\times x_1 +...+row_1\times x_n \\row_2\times x_1 +...+row_2\times x_n\\... \\ row_m\times x_1 +...+row_1\times x_m\end{bmatrix}=\begin{bmatrix}0\\0\\...\\0\end{bmatrix}
$$
这其实就是我们前面讨论的矩阵乘法的理解之一 点乘，每一行与x的点乘都为0，意味着每一行都和x向量垂直，但是A的行空间并不是只有A中的这几行行向量，还有他们的线性组合，也就是说，我们还要证明$(c_1 \text{row}_1 + c_2 \text{row}_2 + \dots) \cdot x = 0$ , 这其实也是显而易见的
$$(c_1 \text{row}_1 + c_2 \text{row}_2 + \dots) \cdot x = c_1 \cdot (\text{row}_1 x) + c_2 \cdot (\text{row}_2 x) + \dots=0$$
由此可以证明A的行空间和零空间是正交的
行空间和零空间正交实际上是把n维空间分成了两个子空间，因为行空间和零空间的维度刚好为n，如果n=3，行空间的维度为2，就像一个平面和法向量！
上面所提到的一分为二实际上 叫 正交补

### Ax=b 无解的解决办法
矩阵的数据是来源与实际测量的，很多时候测量的时候难免会混入坏数据，导致Ax=b解不出来了
有时候测量很多数据之后会导致行数m很多，列数n很少(较少的未知数要满足非常多的方程)，这个时候也可能无解
这个时候我们企图去去掉"坏数据"，来找到解，所以一个方法是不断去掉方程，直到出现一个可逆方程，然后就可以求出解，但这种方法并不完美，因为他破坏了完整的信息
我们希望利用所有的测量值求出最优解，从而得到完整的信息
需要一个至关重要的矩阵 -- $A^TA$ , 这个矩阵方阵，对称
将坏方程两侧乘以$A^T$ 就能得到好方程
这是未来的知识所设计到的核心的方程 $A^TA\hat{x}=A^Tb$ ,这个$\hat{x}$并非Ax=b的解，而是一个最优解
但实际上$A^TA$并不是一定可逆的，如果A的各列线性相关的时候，$A^TA$就不可逆了
### 二维空间投影
二维空间的投影实际上就是向量的投影，或者说是向一维子空间的投影
![Pasted image 20250917092457.png\|400](/img/user/accessory/Pasted%20image%2020250917092457.png)
p是b在a上的投影，p在a向量上，是a的倍数，故令p=xa, b与p的差叫e，e带有误差的意思，e的模在某种程度上表示了b到a的最短距离，而这正是最优解的体现: 误差最小
图中最重要的地方在于 正交的地方
$$
\begin{align}
a^T(b-xa)=0 \\ 
x是个数，a^Tb - x(a^Ta) = 0\\
a^Tb = x(a^Ta), a^Ta是个数 \\
x=\frac{a^Tb}{a^Ta}\\
p=xa=\frac{aa^T}{a^Ta}b
\end{align}
$$
值得注意的是，a是一个列向量，$aa^T$为一列乘一行为一个矩阵，而$a^Ta$为一行乘一列，就是个点乘的数，所以实际上能发现，所谓的投影，实际上是给b作用了一个矩阵P，这个矩阵P就是$\frac{aa^T}{a^Ta}$,我们把这个矩阵叫做投影矩阵
下面来研究投影矩阵的性质
**性质1: rank(P)=1**
这个问题可以这么思考，P右乘一个向量，实际上是对P的列进行了线性变换，实际上列空间不变，也就是说P的列空间和p的列空间应该是一样的，而p是一维的一个沿着a的向量，那么P的秩就为1
**性质2: P是对称的**
**性质3: $P^2=P$** -- 对p再做一次投影，实际上就是b左乘了两次P，而p做投影结果还是那样

### 三维(高维)空间的投影
Why Projection?
之前讨论了实际情况会出现Ax=b无解的情况，所谓的无解就是b没有落在A的列空间中，面对这种情况，我们想找到解，就需要对b变化最小程度的调整并确保调整后的b在A的列空间内，而这个最小程度的变换实际上就是投影，所谓的最小程度就体现在e是垂直距离最短上, 也就是说实际上我们要求解$A\hat{x} = p$ 

![Pasted image 20250917095816.png\|500](/img/user/accessory/Pasted%20image%2020250917095816.png)
假设列空间的一对基是$a_1,a_2$，也就是说A矩阵中的列为$a_1,a_2$
e垂直于这个平面，垂直距离最短
p是b的投影，投影在了列空间上，所以p是列空间中的一个向量，也就是$p=x_1a_1+x_2a_2$, 如果用矩阵的形式写，实际上就是Ax
但注意这个x并不是真正Ax=b中的x，所以我们带个帽子区分，也就是$p=\hat{x_1}a_1+\hat{x_2}a_2=A\hat{x}$
实际上我们的问题是寻找合适的列组合，好让误差向量垂直于这个平面，这是问题的关键，也就是说e分别垂直于$a_1，a_2$
$$
\begin{align}
a_1^T(b-A\hat{x})=0\\
a_2^T(b-A\hat{x})=0
\end{align}
$$
如果表示成矩阵形式
$$
\begin{align}
\begin{bmatrix}a_1^T\\a_2^T\end{bmatrix}(b-A\hat{x})=\begin{bmatrix}0\\0\end{bmatrix}
\end{align}
$$
也就是
$$
A^T(b-A\hat{x})=0
$$
从这个式子能知道，$b-A\hat{x}$在A的左零空间中，而之前学了A的左零空间和A的列空间正交，哈哈哈再次验证了e垂直于A的列空间
解这个方程
$$
A^TA\hat{x} = A^Tb
$$
!!这就是之前提到的求解无解Ax=b的方法，将方程两边都左乘A的转置就会变成一个好方程
$$
\begin{align}
\hat{x}=(A^TA)^{-1}A^Tb\\

\end{align}
$$
其实从这里面也能看出来有解需要$A^TA$可逆，之前提到了，需要A各列线性无关，其实相比原来求解Ax=b是放宽条件了，原来x有解的条件是A可逆，现在有解的条件是A各列线性无关
$$
p=A\hat{x}=A(A^TA)^{-1}A^Tb
$$
这和二维中的$\frac{aa^T}{a^Ta}$是一致的
对称矩阵
$$
P=A(A^TA)^{-1}A^T
$$
这里要注意这个式子是否还能化简的问题，如果A是个可逆矩阵，也就是说A是个方阵，那就能继续化简$P=AA^{-1}(A^T)^{-1}A^T=I$,这是合理的，因为如果A可逆，Ax=b有解，b本来就在A的列空间中，他的投影就是他自己 所以矩阵是I

### 投影矩阵
前面已经讲过投影矩阵$P=A(A^TA)^{-1}A^T$ , 投影矩阵P作用于向量b，会将向量b投影到A的列空间中，现在来看两个极端的例子
**b在矩阵A的列空间中**
所谓的b在矩阵A的列空间中，是说b是矩阵中A的各列的线性组合，而其实在矩阵的乘法中，右乘其实就是对列的线性组合，也就说Ax是对A各列的线性组合，所以如果b在矩阵A的列空间中，意味着Ax=b，那么
$$
Pb=A(A^TA)^{-1}A^Tb = A(A^TA)^{-1}A^TAx=AA^{-1}(A^T)^{-1}A^TAx=AIx=Ax=b
$$
-> b在矩阵A的列空间中 => Pb=b

**b垂直于矩阵A的列空间**
如果A垂直于矩阵A的列空间中，根据前面学的正交补，那么b是A的左零空间中的一个向量，也就是说$A^Tb=0$ 
$Pb=A(A^TA)^-1A^Tb=A(A^TA)^{-1}0=0$

其实从上面的例子能看出来，向量b是能分解成两个向量的，一个向量在A的列空间中，一个向量在A的左零空间中(合理hh)，而对于$P=A(A^TA)^{-1}A^T$这个矩阵是把b投影到A的列空间中来说，就是保留A列空间中的分量p，去掉左零空间中的分量e
![Pasted image 20250920105436.png\|500](/img/user/accessory/Pasted%20image%2020250920105436.png)

b=e+p, e=b-p = b-Pb=(I-P)b -> I-P就是A的左零空间上的投影矩阵！

### 最小二乘法
这是所学的内容的应用，用最小二乘法拟合一条直线
假设b=C+Dt ，确定这条直线也就是确定C和D
![Pasted image 20250917105819.png\|400](/img/user/accessory/Pasted%20image%2020250917105819.png)
这三个点代入这条直线的方程 $\begin{align}C+D=1\\C+2D=2\\C+3D=2\end{align}$ 写成矩阵形式$\begin{bmatrix}1&1\\1&2\\1&3\end{bmatrix}\begin{bmatrix}C\\D\end{bmatrix}=\begin{bmatrix}1\\2\\3\end{bmatrix}$，这显然是无解的
首先我们要明白最小误差是什么，我们定义误差是$A\hat x-b=e$ ， 以误差向量的模长的平方来作为误差和，即$|A\hat{x}-b|^2 = |e|^2 = e_1^2+e_2^2+e_3^2=（C+D-1)^2+(C+2D-2)^2+(C+3D-2)^2$ , 我们要求其最小平方和(最小二乘) -- 比如第一个点我们代入式子我们算到的值是C+D，实际上观测值是1 所以是C+D-1
在线性代数中，$A\hat x$其实也是A的列空间中的向量，那么$A\hat x-b$是列空间的向量与b向量相减，其实就是e向量，如果想让e的模长的平方最小，就是让b对列空间做投影，该投影得到的Pb是列空间中距离b最近的向量，也就是说，其实是求解$A \hat x=Pb$!
$$
\begin{align}
A\hat{x}=Pb=A(A^TA)^{-1}A^Tb\\
方程两边同乘以A^T有: \space A^TA(A^TA)^{-1}A^Tb = A^Tb\\
A^TA\hat x =A^T b
\end{align}
$$
然后求得$A^TA,A^Tb$，能够得到
$$
\begin{bmatrix}3 & 6\\6&14\end{bmatrix}\begin{bmatrix}\hat C\\ \hat D\end{bmatrix}=\begin{bmatrix}5\\11\end{bmatrix}
$$
写成方程就是
$$
\begin{align}
3\hat C + 16 \hat D =5\\
6\hat C + 14 \hat D =11
\end{align}
$$
呃呃其实你会发现这个式子和对上面的最小二乘求偏导得到的结果是一样的！
也就是说这是等价的！

### 标准正交向量组
有一种线性无关的情况是比较特殊的：互相垂直的各列一定是线性无关的
更特殊的，如果垂直的是单位向量(标准正交)，比如$\begin{bmatrix}1\\0\\0\end{bmatrix},\begin{bmatrix}0\\1\\0\end{bmatrix},\begin{bmatrix}0\\0\\1\end{bmatrix}$,这些向量所组成的向量组一般被称为标准正交向量组，标准正交向量组中的向量互相垂直（正交）且为单位向量（标准）
同样标准正交向量还有$\begin{bmatrix}cos 𝜃 \\sin 𝜃\end{bmatrix},\begin{bmatrix}-sin 𝜃 \\cos 𝜃\end{bmatrix}$

### 标准正交向量与正交矩阵
标准正交向量组可以用下面这个式子表示
$$
\displaylines{
q_{i}^{T} q_{j}\left\{\begin{array}{l}{0 \ldots . .(i \neq j)} \\ {1 \ldots . .(i=j)}\end{array}\right.
}
$$
这实际上就是之前所说的，不同向量之间是正交的，向量长度为1
为什么要研究标准正交向量？ -- 是因为标准正交向量能够极大简化计算(后面会体会到)
之前我们学过，一个理解矩阵的方法就是一组向量，那如果把标准向量组的向量都放到一起，就是一个矩阵，这个矩阵叫做标准正交矩阵Q
$$
Q=\begin{bmatrix}q_1 &q_2 & ... &q_n \end{bmatrix}
$$
这样的标准正交矩阵有一个很好的性质
$$
\displaylines{
Q^TQ=\begin{bmatrix}q_1^T\\q_2^T\\\vdots\\q_n^T\end{bmatrix}\begin{bmatrix}q_1&q_2&\cdots&q_n\end{bmatrix}=\begin{bmatrix}1& 0& \cdots& 0\\0& 1& \cdots& 0\\\vdots& \vdots& \ddots& \vdots\\0& 0& \cdots& 1\end{bmatrix}=I
}
$$
如果Q是方阵时，我们将这样的矩阵Q叫做正交矩阵
如果Q是方阵，那么Q就有逆矩阵，那么由于$Q^TQ=I$, $Q^{-1}=Q^T$

标准正交矩阵Q能够极大的简化计算，比如在前面提到的投影矩阵中
$$
\displaylines{
P=A(A^TA)^{-1}A^T\xrightarrow{A是标准正交矩阵Q}P=QQ^T\xrightarrow{A如果还是方阵（也即A是正交矩阵）}P=I
}
$$
其实也很好理解，如果Q是方阵，那么实际上这个列空间就是整个空间了，投影矩阵就肯定是I了
在我们讲过的方程$A^TA\hat x=A^Tb$中，如果A是标准正交矩阵，这个方程就变成了$Q^TQ \hat x = Q^Tb$，也就是$\hat x = Q^Tb$,分解来看就是$\hat{x_i}=q_i^Tb$ 

不过这里问题是对于一个普通矩阵A变成Q，用的是我们后面讲的方法--施密特正交化，在化简前后的A和Q是等价的(后面会讲), 所以对于上面这个方程来说，两个方程的$\hat x$的解实际上是一样的（其实QR分解也能说明这个问题）

### Gram-Schmidt 正交化
如何将一个普通的线性无关的向量组或者矩阵 转换成 标准正交基呢？ -- Gram-Schmidt正交化
$$
\displaylines{
线性无关向量a,b\xrightarrow{Graham}正交向量A,B\xrightarrow{Schmidt}标准正交向量q_1=\frac{A}{\|A\|},q_2=\frac{B}{\|B\|}
}
$$
教授是从两个向量开始介绍的
![Pasted image 20250922181751.png\|200](/img/user/accessory/Pasted%20image%2020250922181751.png)
假设有两个普通的向量a和b
想将这两个向量转换成相互正交的向量，用到了我们之间所学的投影，可以将a向量直接作为一个正交基向量A，然后用b向量把B表示出来，而这个表示实际上就是投影中所讲的e
$$e=B=b-p=b-Pb=b-A(A^TA)^{-1}A^Tb$$
由于上述公式中A,B指的都是向量而不是矩阵，所以有$B=b-\frac{AA^Tb}{A^TA}$,$AA^T$为一列乘一行为一个矩阵，而$A^TA$为一行乘一列，就是个点乘的数, 而$A^Tb$也是一个点乘，一个数，让他们先算作为系数，就变成了$B=b-\frac{A^Tb}{A^TA}A$
检查一下是否$A\bot B$
$$
\displaylines{
A^TB=A^Tb-A^T\frac{A^Tb}{A^TA}A=A^Tb-\frac{A^TA}{A^TA}A^Tb=0
}
$$
AB正交之后，就剩下简单的单位化了
同样的道理，如果我们有三个线性无关的向量a,b,c. 按照流程实际上只是c减去的是其在A和B上的分量，就能得到与A，B正交的C
$$
\displaylines{
C=c-\frac{A^Tc}{A^TA}A-\frac{B^Tc}{B^TB}B
}
$$

example
$a=\begin{bmatrix}1\\1\\1\end{bmatrix}, b=\begin{bmatrix}1\\0\\2\end{bmatrix}$,求正交矩阵Q
$$
\displaylines{
首先,\quad A=a=\begin{bmatrix}1\\1\\1\end{bmatrix}\\
根据公式有,\quad B=b-\frac{A^Tb}{A^TA}A=\begin{bmatrix}1\\0\\2\end{bmatrix}-\frac{3}{3}\begin{bmatrix}1\\1\\1\end{bmatrix}=\begin{bmatrix}0\\-1\\1\end{bmatrix}\\
验证正交性有,\quad A^TB=0\\
最后再进行单位化,\quad q_1=\frac{1}{\sqrt 3}\begin{bmatrix}1\\1\\1\end{bmatrix},\quad q_2=\frac{1}{\sqrt 2}\begin{bmatrix}1\\0\\2\end{bmatrix}\\
则标准正交矩阵为\quad Q=\begin{bmatrix}\frac{1}{\sqrt 3}& 0\\\frac{1}{\sqrt 3}& -\frac{1}{\sqrt 2}\\\frac{1}{\sqrt 3}& \frac{1}{\sqrt 2}\end{bmatrix}
}
$$
对比原始矩阵$A=\left[\begin{array}{ll}{1} & {1} \\ {1} & {0} \\ {1} & {2}\end{array}\right]$和标准正交矩阵$Q=\left[\begin{array}{cc}{1 / \sqrt{3}} & {0} \\ {1 / \sqrt{3}} & {-1 / \sqrt{2}} \\ {1 / \sqrt{3}} & {1 / \sqrt{2}}\end{array}\right]$,可以发现实际上他们表示的还是同一个列空间，实际上从公式中也能看出来，$B=b-\frac{A^Tb}{A^TA}A$实际上只是对A(实际上就是a)和b做了线性变换
实际上，这实际上就像是旋转了坐标轴！

在施密特正交化时的两个建议
![Pasted image 20260527144256.png\|500](/img/user/accessory/Pasted%20image%2020260527144256.png)
不要把分母往里带  标准化之前检查是否垂直
在标准化的还有第二个向量不用算分数了 而且向量可以把公因子也扔出去
一个好记的施密特正交化的方式是
![Pasted image 20260529213557.png\|300](/img/user/accessory/Pasted%20image%2020260529213557.png)
分母放$α_1$或者说$β_1$坐标平方和 分子放$α_1$或者说$β_1$和$α_2$的点乘


### QR分解
之前我们曾经学过用矩阵的视角来审视消元法，得到了A=LU，如果用同样的方法来审视Gram-Schmidt正交化，有A=QR
实际上是右乘了一个三角矩阵啊，实际上右乘就是列变换啊，这也能说明列空间并没有发生改变
$\Bigg[a_1\ a_2\Bigg]=\Bigg[q_1\ q_2\Bigg]\begin{bmatrix}a_1^Tq_1& a_2^Tq_1\\a_1^Tq_2& a_2^Tq_2\end{bmatrix}$



### 