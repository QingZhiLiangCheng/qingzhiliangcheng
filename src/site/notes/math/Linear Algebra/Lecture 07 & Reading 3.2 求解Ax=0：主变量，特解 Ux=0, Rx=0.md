---
{"created":"2025-09-01T16:01:32.079+08:00","updated":"2025-09-03T20:28","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 07 & Reading 3.2 求解Ax=0：主变量，特解 Ux=0, Rx=0/","dgPassFrontmatter":true,"noteIcon":""}
---


today's agenda
turning the idea of the definition into an algorithm -- 求解Ax=0的算法 -- 求零空间
$$
A=\begin{bmatrix}
1 & 2 &2 &2 \\
2&4 &6&8\\
3&6&8&10
\end{bmatrix}
$$
仍然是[[math/Linear Algebra/Lecture 02 & Reading 2.2, 2.3 矩阵消元法求解方程组\|消元法]]
$$
A=\begin{bmatrix}
1 & 2 &2 &2 \\
2&4 &6&8\\
3&6&8&10
\end{bmatrix} =
\begin{bmatrix}
\underline{1}&2&2&2\\0& 0&2&4\\0&0 & 2 &4
\end{bmatrix}=
\begin{bmatrix}
1&2&2&2\\0& 0&\underline{2}&4\\0&0 & 0 &0
\end{bmatrix}
$$
方法仍然是选取pivot(主元, 标下划线了) 做行变换 最后得到一个行阶梯矩阵U
主元的数量 -- the most important number about this matrix -- the rank of matrix矩阵的秩
主元所在的列叫做pivot columns, 其余的列叫做free columns
在这个例子中，U的column 1, column 3为pivot columns(主元列), column 2, column 4为free columns(自由列)，所谓自由，是因为可以自由或任意分配数值
事实上，上面U所代表的方程组是
$$
\begin{align}
x_1+2x_2+2x_2+2x_2 = 0\\
2x_3+4x_4=0
\end{align}
$$
当自由列决定了，也就意味着代入也就决定了主元列
比如，可以去自由列中的自由变量$x_2=1$, $x_4=0$ , 这样就能得到一个解 $\begin{bmatrix}-2\\1 \\ 0 \\0\end{bmatrix}$，这个解其实很简单就能验证，因为A中第二列是第一列的两倍
而这就是Ax=0的一个零空间，想要得到零空间中的更多向量，显然可以将这个向量乘以一个常数c，即$c\begin{bmatrix}-2\\1 \\ 0 \\0\end{bmatrix}$, 这表示了四维空间中的一条直线
但这并不是零空间中的所有向量，因为自由变量有两个
x2, x4可以取1,0 这是自由，但是也可以取0,1 , 这样可以得到一个解$\begin{bmatrix}2\\0 \\ -2 \\1\end{bmatrix}$
事实上这两个向量的线性组合就是整个零空间. 即$c_1\begin{bmatrix}-2\\1 \\ 0 \\0\end{bmatrix}+c_2\begin{bmatrix}2\\0 \\ -2 \\1\end{bmatrix}$
而这两个解叫做特解，教授叫特解，其实就是特定的解，我们好像一般叫基础解系。因为里面的x2, x4是取的特定的值，通常我们是轮着选取其中一个为1，其余为0
有多少个特解？ 有多少个自由列就有多少个特解，那有多少自由列呢？ 事实上是列数-r，这个数我们成为自由度

为什么有多少自由列就有多少特解？其实这个问题就是为什么自由列能决定解空间的维度，是因为解空间的维度是在满足所有方程结束后, 剩余的可自由移动的方向数量，自由变量定义了自由度，每个自由变量可以独立赋值，而每一种赋值就代表了解空间中的一种“移动方向”，而主变量是被决定的，他们的方向是取决于方程组和自由变量的选择的
？还是不懂

为了让矩阵更干净，可以化简为最简行阶梯R rref -- 向上消元 -- zero above and below the privots, and simplify it. -- 包含所有信息的最简形式 -- privot columns, free columns, 单位阵, ...
![Pasted image 20250901175056.png|500](/img/user/accessory/Pasted%20image%2020250901175056.png)

上面问题的答案可能是下面这个解释吧
$$
Ax=RN=\begin{bmatrix}I&F\\0&0\end{bmatrix}\begin{bmatrix}x_{pivot}\\x_{free}\end{bmatrix}=0
$$
所以有
$$
x_{pivot}+Fx_{free}=0
$$
令自由变量为I，则有$x_{pivot}=-F$
$$
N=\begin{bmatrix}-F \\I\end{bmatrix}
$$
所以事实上I的维度就是自由度的个数