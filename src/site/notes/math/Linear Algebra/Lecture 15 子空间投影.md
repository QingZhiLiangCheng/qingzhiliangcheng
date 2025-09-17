---
{"created":"2025-09-17T08:40","updated":"2025-09-17T11:03","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 15 子空间投影/","dgPassFrontmatter":true,"noteIcon":""}
---

### 二维空间的投影
二维空间的投影实际上就是向量的投影，或者说是向一维子空间的投影
![Pasted image 20250917092457.png|400](/img/user/accessory/Pasted%20image%2020250917092457.png)
p是b在a上的投影，p在a向量上，是a的倍数，故令p=xa, b与p的差叫e，e带有误差的意思，e的模在某种程度上表示了b到a的最短距离，而这正是最优解的体现: 误差最小
图中最重要的地方在于 正交的地方
$$
\begin{align}
a^T(b-xa)=0 \\ 
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
上一节课讨论了实际情况会出现Ax=b无解的情况，所谓的无解就是b没有落在A的列空间中，面对这种情况，我们想找到解，就需要对b变化最小程度的调整并确保调整后的b在A的列空间内，而这个最小程度的变换实际上就是投影，所谓的最小程度就体现在e是垂直距离最短上, 也就是说实际上我们要求解$A\hat{x} = p$ 

![Pasted image 20250917095816.png|500](/img/user/accessory/Pasted%20image%2020250917095816.png)
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
从这个式子能知道，$b-A\hat{x}$在A的左零空间中，而上一节课学了A的左零空间和A的列空间正交，哈哈哈再次验证了e垂直于A的列空间
解这个方程
$$
A^TA\hat{x} = A^Tb
$$
!!这就是上节课提到的求解无解Ax=b的方法，将方程两边都左乘A的转置就会变成一个好方程
$$
\begin{align}
\hat{x}=(A^TA)^{-1}A^Tb\\

\end{align}
$$
其实从这里面也能看出来有解需要$A^TA$可逆，上节课提到了，需要A各列线性无关，其实想比原来求解Ax=b是放宽条件了，原来x有解的条件是A可逆，现在有解的条件是A各列线性无关
$$
p=A\hat{x}=A(A^TA)^{-1}A^Tb
$$
这和二维中的$\frac{aa^T}{a^Ta}$是一致的
对称矩阵
$$
P=A(A^TA)^{-1}A^T
$$
这里要注意这个式子是否还能化简的问题，如果A是个可逆矩阵，也就是说A是个方阵，那就能继续化简$P=AA^{-1}(A^T)^{-1}A^T=I$,这是合理的，因为如果A可逆，Ax=b有解，b本来就在A的列空间中，他的投影就是他自己 所以矩阵是I

### 最小二乘初涉
这是所学的内容的应用，用最小二乘法拟合一条直线
![Pasted image 20250917105819.png|400](/img/user/accessory/Pasted%20image%2020250917105819.png)
假设b=C+Dt ，确定这条直线也就是确定C和D
这三个点代入这条直线的方程
$$
\begin{align}
C+D=1\\
C+2D=2\\
C+3D=2
\end{align}
$$
显然求不出来
写成矩阵形式
$$
\begin{bmatrix}1&1\\1&2\\1&3\end{bmatrix}
\begin{bmatrix}C\\D\end{bmatrix}=
\begin{bmatrix}1\\2\\3\end{bmatrix}
$$
能通过左乘$A^T$ 就能有解，得到最优解，得到最理想的投影