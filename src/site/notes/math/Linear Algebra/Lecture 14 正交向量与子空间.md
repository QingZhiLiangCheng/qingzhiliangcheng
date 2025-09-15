---
{"created":"2025-09-15T14:35","updated":"2025-09-15T17:23","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 14 正交向量与子空间/","dgPassFrontmatter":true,"noteIcon":""}
---

### 向量正交
什么是两个向量正交？
正交是垂直的另一种说法，意味着在n维空间中，向量的夹角为90度
如果两个向量正交，垂直意味着点积为0，如果用矩阵乘法来表示的话，就是$x^Ty=0$
![Pasted image 20250915163113.png|200](/img/user/accessory/Pasted%20image%2020250915163113.png)

下面来证明这个结论
勾股定理有${|x|}^2+{|y|}^2={|x+y|}^2$, 怎么理解这个$|x|^2$? 实际上就是x和自己的点积，就是$x^Tx$
那么这个式子就变成了$x^Tx+y^Ty=(x+y)^T(x+y)$ , 等号右侧转置符号进去得$(x^T+y^T)(x+y)$, 展开得$x^Tx+x^Ty+y^Tx+y^Ty$（矩阵的运算律)， 化简得 $0=x^Ty+y^Tx$
由于$x^Ty=y^Tx$ (事实上都是$x_i$和$y_i$的乘积和)，所以得到了$x^Ty=0$
另外多一嘴，如果x和y中其中一个为零向量，那么任意另一个向量可以为任何值，也就是说，零向量和任何向量都正交

### 空间的正交
什么叫空间的正交？子空间S和子空间T正交是什么意思？
子空间S和子空间T正交意味着every vectors in S is orthogonal to every vectors in T.
这里注意一个特别容易混淆的情况，事实上xoy面和yoz面不是正交的，如果xoy面取两个面的交界线(也就是x轴)，yoz面选择一条和交界线平行的向量，那么这两个向量就不垂直，而是平行的
这也说明，如果两个子空间正交，一定不会交于某个非零向量

### 矩阵的四个子空间的正交
![Pasted image 20250915164810.png|500](/img/user/accessory/Pasted%20image%2020250915164810.png)
这张图是我们之前讨论过的四种子空间以及他们的维数，除此之外，这张图还展示了零空间和行空间是正交的，列空间和左零空间是正交的
这里以row space is orthogonal to the null space为例来看一下
对于Ax=0，事实上给出了行空间和零空间的所有信息
$$
Ax=\begin{bmatrix}row_1 \space of \space A\\row_2 \space of \space A\\...\\row_m \space of \space A\end{bmatrix}
\begin{bmatrix}x_1\\...\\x_n\end{bmatrix}=
\begin{bmatrix}row_1\times x_1 +...+row_1\times x_n \\row_2\times x_1 +...+row_2\times x_n\\... \\ row_mtimes x_1 +...+row_1\times x_m\end{bmatrix}=\begin{bmatrix}0\\0\\...\\0\end{bmatrix}
$$
这其实就是我们前面讨论的矩阵乘法的理解之一 点乘，每一行与x的点乘都为0，意味着每一行都和x向量垂直，但是A的行空间并不是只有A中的这几行行向量，还有他们的线性组合，也就是说，我们还要证明$c_1 row_1^T + c_2 row_2^T + ... = (c_1 row_1+c_2 row_2)^T=0$ , 这其实也是显而易见的
由此可以证明A的行空间和零空间是正交的

行空间和零空间正交实际上是把n维空间分成了两个子空间，因为行空间和零空间的维度刚好为n，如果n=3，行空间的维度为2，就像一个平面和法向量！
上面所提到的一分为二实际上 叫 正交补


### "solve" Ax=b when there is no solution
矩阵的数据是来源与实际测量的，很多时候测量的时候难免会混入坏数据，导致Ax=b解不出来了
有时候测量很多数据之后会导致行数m很多，列数n很少(较少的未知数要满足非常多的方程)，这个时候也可能无解
这个时候我们企图去去掉"坏数据"，来找到解，所以一个方法是不断去掉方程，知道出现一个可逆方程，然后就可以求出解，但这种方法并不完美，因为他破坏了完整的信息
我们希望利用所有的测量值求出最优解，从而得到完整的信息
需要一个至关重要的矩阵 -- $A^TA$ , 这个矩阵方阵，对称
hhh教授说现在说这个有点早了
将坏方程两侧乘以$A^T$ 就能得到好方程
这是未来的知识所设计到的核心的方程 $A^TA\hat{x}=A^Tb$ ,这个$\hat{x}$并非Ax=b的解，而是一个最优解
但实际上$A^TA$并不是一定可逆的，如果A的各列线性相关的时候，$A^TA$就不可逆了
证明需要用到$N(A^TA)=N(A)$和rank of $A^TA$ = rank of A
后面会给出证明