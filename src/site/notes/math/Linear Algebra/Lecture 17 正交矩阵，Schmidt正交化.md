---
{"created":"2025-09-22T15:35","updated":"2025-09-22T18:35","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 17 正交矩阵，Schmidt正交化/","dgPassFrontmatter":true,"noteIcon":""}
---

### 标准正交向量与正交矩阵
上个lecture介绍过标准正交向量组，标准正交向量组可以用下面这个式子表示
$$
\displaylines{
q_{i}^{T} q_{j}\left\{\begin{array}{l}{0 \ldots . .(i \neq j)} \\ {1 \ldots . .(i=j)}\end{array}\right.
}
$$
这实际上就是上个lecture所说的，不同向量之间是正交的，向量长度为1
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
如何将一个普通的现行无关的向量组或者矩阵 转换成 标准正交基呢？ -- Gram-Schmidt正交化
$$
\displaylines{
线性无关向量a,b\xrightarrow{Graham}正交向量A,B\xrightarrow{Schmidt}标准正交向量q_1=\frac{A}{\|A\|},q_2=\frac{B}{\|B\|}
}
$$
教授是从两个向量开始介绍的
![Pasted image 20250922181751.png|200](/img/user/accessory/Pasted%20image%2020250922181751.png)
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


### QR分解
之前我们曾经学过用矩阵的视角来审视消元法，得到了A=LU，如果用同样的方法来审视Gram-Schmidt正交化，有A=QR
实际上是右乘了一个三角矩阵啊，实际上右乘就是列变换啊，这也能说明列空间并没有发生改变
$\Bigg[a_1\ a_2\Bigg]=\Bigg[q_1\ q_2\Bigg]\begin{bmatrix}a_1^Tq_1& a_2^Tq_1\\a_1^Tq_2& a_2^Tq_2\end{bmatrix}$
