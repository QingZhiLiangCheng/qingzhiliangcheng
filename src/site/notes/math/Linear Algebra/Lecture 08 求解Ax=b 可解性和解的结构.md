---
{"created":"2025-09-03T19:47","updated":"2025-09-03T22:18","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 08 求解Ax=b 可解性和解的结构/","dgPassFrontmatter":true,"noteIcon":""}
---

满秩
列满秩 r=n 没有自由变量  N(A) = zero vector x=xp -- 如果解存在 解唯一 要么无解
行满秩 r=m  没有零行 对任意b都有解 
r=n=m  可逆矩阵 R=I 零空间 0向量 b什么要求都没有

today's agenda: 求解Ax=b 有解还是无解？ 有解的话是唯一解还是无数解？
仍然还是[[math/Linear Algebra/Lecture 07 & Reading 3.2 求解Ax=0：主变量，特解 Ux=0, Rx=0\|上个Lecture]]的例子
$$
\begin{align}
x_1+2x_2+2x_3+2x_4=b_1 \\
2x_1+4x_2+6x_3+8x_4=b_2 \\
3x_1+6x_2+8x_3+10x_4=b_3
\end{align}
$$
仍然还是消元法，之前学过，消元法实际本质上就是做的是行变换，同样的变换要作用在b上面，这里直接写到一个矩阵里，这个矩阵就是增广矩阵
$$
\left[
\begin{array}{cccc|c}
1&2&2&2& b1 \\ 2 &4&6&8&b_2 \\ 3&6&8&10&b_3
\end{array}
\right]
$$
处理成 -- 行阶梯型矩阵
$$
\left[
\begin{array}{cccc|c}
1&2&2&2& b1 \\ 2 &4&6&8&b_2 \\ 3&6&8&10&b_3
\end{array}
\right] = 
\left[
\begin{array}{cccc|c}
1&2&2&2& b1 \\ 0&0&2&4&b_2-b_1 \\ 0&0&0&0&b_3-b_2-b_1
\end{array}
\right]
$$
其实很明显最后一行所对应的方程是$0x_1+0x_2+0x_3+0x_4=b_3-b_2-b_1$ 也就是说，要想Ax=b有解，$b_3-b_2-b_1$需要等于0 -- 换句话说，如果A各行的线性组合得到零行  b同样组合也必须为零。
再换句话说就是b要在A的列空间中
这两句话是等价的 
具体求解的算法是这样的
- 找particular solution(特解) -- 一个好的方法 将自由变量设置为0 求出Ax=b中的主变量
- 加上Ax=0的零空间

对于一个具体的例子是这样的
![ee1a9705347331ea31c3c4411cc52d46.jpg|400](/img/user/accessory/ee1a9705347331ea31c3c4411cc52d46.jpg)
其实特解+零空间是合理的，因为
$$
\begin{align}
Ax_{null sapce}=0\\
Ax_{special}=b
\end{align}
\to A(x_{nullspace}+x_{special})=b
$$
对于上面的例子，其实能发现最后的解的空间实际上是类似四维子空间的一个空间，后面的零空间是一个子空间，然后子空间的每个向量都加上了一个特解的向量，构成了一个新的平面（就像平移了一样）

上一个Lecture提到过秩的定义，当时提到的秩实际上就是主元变量或者说主元列的个数
这里更加深入的来探讨了对于不同秩的矩阵A的Ax=b的可解性
对于一个m by n的矩阵，秩为r

对于列满秩的一个矩阵A, 也就是说有多少列就有多少个主元，比如
$$
\begin{bmatrix} 1 &2 \\2&8 \\3&10 \end{bmatrix}\to
\begin{bmatrix} 1 &2 \\0&4 \\0&0 \end{bmatrix}
$$
由于上个lecture的笔记中其实提到过，自由变量的个数实际上是零空间的维度，所以其实对于Ax=0来说，零空间只有零向量，那么最后的通解就取决于Ax=b有没有特解
- 如果有特解，那么解是唯一的
- 如果没有特解，那就是无解

对于行满秩的一个矩阵A, 也就是说有都少行就有多少个主元，比如
$$\begin{bmatrix}1 &2&2&2\\2&4&6&8\end{bmatrix} \to
\begin{bmatrix}1 &2&2&2\\0&0&1&2\end{bmatrix}
$$
在消元过程中不会出现零行 所以对b实际上没有要求，b取什么都会在A中的列空间中
同样的，根据上个Lecture的内容，Ax=0存在零空间
所以对于行满秩实际上是无数解

对于r=n=m的矩阵 -- 满秩方阵 比如
$$
\begin{bmatrix}
1&2\\3&1
\end{bmatrix}
$$
最终的最简行阶梯型矩阵一定是I，同样的，零空间是zero vector
所以Ax=b 肯定有解 且是唯一解



