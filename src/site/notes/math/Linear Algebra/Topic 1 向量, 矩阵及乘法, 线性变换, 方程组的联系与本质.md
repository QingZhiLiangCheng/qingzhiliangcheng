---
{"created":"2025-12-01T10:04","updated":"2025-12-02T14:53","dg-publish":true,"permalink":"/math/Linear Algebra/Topic 1 向量, 矩阵及乘法, 线性变换, 方程组的联系与本质/","dgPassFrontmatter":true,"noteIcon":""}
---

### 向量(Vectors)
向量是中学知识中的一个概念。我们在中学时期接触过了向量的模, 加法, 减法, 数乘, 点积(Dot Products)... 但我们的接触仅仅停留在二维和三维阶段，并且二维和三维实际上是我们能够画出来的（因为我们是三维生物），在线性代数中，需要抽象到高维……（想起来一本书《平面国》hh）
向量在代数视角实际上就是一个具有特殊含义的有序的数集，将分散的数值按固定逻辑排序。eg: 具有特殊含义的一条记录(a,b,c)=(1,2,3)就是一个向量。
在几何视角向量实际上就是空间中的有向箭头，通常我们用空间中的一个点的坐标来表示向量，比如向量$\begin{bmatrix}2\\3\end{bmatrix}$ 就是xy平面上从原点到(2,3)的有向线段，实际上是其在直角坐标系(正交基底)上的分量
我觉得向量本质上有三种运算：加法，数乘，点乘
通过加法和数乘，能够对向量做线性组合(Linear Combinations). 我们可以通过数乘给向量做伸缩变换，而对向量伸缩变化再相加 eg$\vec{v} = a\hat{\mathbf{x}} + b\hat{\mathbf{y}} + c\hat{\mathbf{z}}$ 这种若干向量按比例相加就叫线性组合 -> 所以对于任意的a,b,c 的一个取值，就是xyz空间的一个点，如果a,b,c合法的话（会有特殊情况），那对于任意的abc取值的所有组合，我们就能构成整个xyz plan.
点积运算运算事实上就是一个数，但这个数和向量的length还有向量间的angle有关系
### 矩阵(Matrix)的本质
#### 理解1: 向量的堆叠
矩阵实际上可以看作是将一堆行矩阵或者是一堆列矩阵罗列在了一起，然后可以对一组向量进行统一的操作，比如对这一组的每一个都进行伸缩变换，这一点在后面会再进行介绍
#### 理解2：方程组
这里设计到方程组的row picture和column picture
首先来看方程组的row picture，所谓的row picture其实就是中学所接触的空间中直线的交点
$$ \begin{cases} x - 2y = 1 \\ 3x + 2y = 11 \end{cases} $$
![Pasted image 20250728110957.png|300](/img/user/accessory/Pasted%20image%2020250728110957.png)

column picture的视角是从 "vector equation" 的角度
$$
x\begin{bmatrix}1\\3\end{bmatrix} + y\begin{bmatrix}-2\\2\end{bmatrix}=
\begin{bmatrix}1\\11\end{bmatrix}=
b
$$
这其实是两个向量的线性组合
![Pasted image 20250728111517.png|400](/img/user/accessory/Pasted%20image%2020250728111517.png)

如果把初始的这两个向量放到一起(其实就是理解1： 矩阵是向量的堆叠），实际上就是一个系数矩阵
$$
\begin{align}
A=\begin{bmatrix}1 & -2\\3 & 2\end{bmatrix}\\
Ax=b\\
\begin{bmatrix}1 & -2\\3 & 2\end{bmatrix}
\begin{bmatrix}x\\y\end{bmatrix}=
\begin{bmatrix}1\\11\end{bmatrix}
\end{align}
$$
这样矩阵和方程实际上就联系起来了
#### 理解3: 一种线性变换的描述
首先什么是线性变换(Linear Transformation)？
变换(transformation)事实上就相当于一种函数(function),输入内容经过处理就对应一个结果，在线性代数中，输入的一个向量，输出的是一个向量的变换。这里之所以叫变换，是因为变换这个词给人一种可视化的感觉。
线性是说：1. 直线变换后仍然是直线 2. 原点不变位置
以二维平面xy平面为例，我们平时的视角就是直角坐标系，基底是$\hat{\imath} = \begin{bmatrix}1 \\ 0\end{bmatrix} , \hat{\jmath} = \begin{bmatrix}0 \\ 1\end{bmatrix}$，事实上，平面中的任何一个向量都能以i和j为基底表示。换句话说，i和j的线性组合能覆盖整个xy平面，即$\hat{v} = a \hat{\imath} + b\hat{\jmath}$
事实证明，网格线保持平行等距离分布的性质有一个重要的推论 就是变换后的线性组合的关系是不变的，其实变换后的i和j是新的基底，$Transformed\hat{v} = a (Transformed\hat{\imath}) + b(Transformed\hat{\jmath})$
-> 这意味着其实可以只根据变换后的i和j就能判断出变换后的v
-> 所以我们只需要记录i和j变换后的位置
所以假设$\hat{\imath} \to \begin{bmatrix}1 \\ -2\end{bmatrix} , \hat{\jmath} \to \begin{bmatrix}3 \\ 0\end{bmatrix} ,\hat{v} = -1\hat{\imath} + 2\hat{\jmath}$ 那么 $\begin{bmatrix}-1 \\ 2\end{bmatrix} \to -1\begin{bmatrix}1 \\ -2\end{bmatrix} + 2\begin{bmatrix}3 \\ 0\end{bmatrix} = \begin{bmatrix}5 \\ 2\end{bmatrix}$
如果把i和j变换后的位置这两个向量罗列在一起，表示成一个矩阵
$$
\begin{bmatrix}1 & 3\\ -2&0\end{bmatrix}
$$
这个矩阵就是一种线性变换的描述，这个矩阵蕴含着一种线性变换的信息，具体来说就是基向量(坐标轴)变换后的位置，这就像函数中的对应法则一样f(x)一样，输入一个向量，就能按照这个矩阵所描述的线性变换输出一个变换后的向量。
$$
\begin{bmatrix}1 & 3\\ -2&0\end{bmatrix} \begin{bmatrix}x \\ y\end{bmatrix} = x\begin{bmatrix}1 \\ -2\end{bmatrix} + y\begin{bmatrix}3 \\ 0\end{bmatrix}=\begin{bmatrix}1x+3y \\ -2x+0y\end{bmatrix}
$$
### 矩阵乘法的理解
$$
\begin{align}
A =
\begin{bmatrix}
1 & 0 & 2 \\
-1 & 3 & 1
\end{bmatrix} ,
B =
\begin{bmatrix}
2 & 1 & 0 & -1 \\
3 & 0 & 1 & 2 \\
1 & -1 & 2 & 0
\end{bmatrix}\\

\\C=AB =
\begin{bmatrix}
4 & -1 & 4 & -1 \\
8 & -2 & 5 & 7
\end{bmatrix}
\end{align}

$$
### 理解1：计算 一行成一列 点乘
矩阵乘法的第一个理解，应该就是我们所学的计算的方法，比如如$C_{1,2}$ 事实上是由A中的第1行和B中的第2列点乘的一个结果
这属于单个元素的计算，有助于求解，应对考试
#### 理解2: 右乘列变换
这其实是对应了矩阵是列向量堆叠的这种思路
右乘行列变换的视角是说，右边矩阵B的四个列向量实际上是描述了左边A矩阵3个列向量的线性组合四种不同的分量，比如B的第一列描述的是A中第一个列向量的2倍+第二个列向量的3倍+第三个列向量的1倍
#### 理解3: 左乘行变换
这其实是对应了矩阵是行向量堆叠的这种思路
左乘行变换的视角是说，左边矩阵A的两个行向量实际上是描述了右边矩阵B的三个行向量的线性组合的两种不同分量，比如A的第一行表描述的是B的三个行向量按照(1,0,2)进行线性组合
#### 理解：一行乘一列
这里的想法是，一行乘一列实际上就是一个矩阵
比如
$$
\begin{bmatrix}1 \\ -1\end{bmatrix}
\begin{bmatrix}2 & 1 & 0 & -1\end{bmatrix}=
\begin{bmatrix}
2 & 1 &0 &-1 \\
-2 & -1 & 0 &1
\end{bmatrix}
$$
而三个一行成一列叠加在一起，就是C矩阵

#### 理解：对向量的堆叠线性变换
这是从前面矩阵是线性变换和矩阵是列向量堆叠的角度来理解的
比如下面这个例子
$$
\begin{bmatrix}1 & 1\\ 0&1\end{bmatrix}
\begin{bmatrix}0 & -1\\ 1&0\end{bmatrix}=
\begin{bmatrix}1 & -1 \\ 1 & 0\end{bmatrix}
$$
可以把左边的这个矩阵看做是一种线性变换的描述(剪切，这其实就是斜二测画法的变换的矩阵描述)，把右边的这个矩阵看作是列向量的堆叠
实际上就是对$\begin{bmatrix}0\\1\end{bmatrix}$进行$\begin{bmatrix}1 & 1\\ 0&1\end{bmatrix}$所描述的线性变换，变为$\begin{bmatrix}1\\1\end{bmatrix}$，$\begin{bmatrix}-1\\0\end{bmatrix}$进行$\begin{bmatrix}1 & 1\\ 0&1\end{bmatrix}$所描述的线性变换，还为$\begin{bmatrix}-1\\0\end{bmatrix}$(x在线性变换中没变，而这个向量只在x有分量)
然后把两个向量还罗列在一个矩阵中就是矩阵乘法的结果
#### 理解: 线性变换的复合
这对应的是矩阵是一种线性变换的描述的视角
很多时候我们有很多种变换，比如先旋转，再剪切
$$
\begin{bmatrix}1 & 1\\ 0&1\end{bmatrix}
\left( 
\begin{bmatrix}0 & -1\\ 1&0\end{bmatrix}
\begin{bmatrix}x\\ y\end{bmatrix}
\right)
$$
有趣的是这很像$f(g(x))$ 也就是其实这是从右往左读的 -- 事实上是ABC是B先作用与C，然后A在作用
既然这是两次线性变换，那直观上就知道一定存在一种线性变换能够一次性起到这两次线性变换的效果
事实上
$$
\begin{bmatrix}1 & 1\\ 0&1\end{bmatrix}
\begin{bmatrix}0 & -1\\ 1&0\end{bmatrix}=
\begin{bmatrix}1 & -1 \\ 1 & 0\end{bmatrix}
$$
这里的理解实际上就是对第一次线性变换完的基底又进行了一次线性变换
一方面，一个矩阵可以表示线性变换的复合！
另一方面，实际上这也间接证明了矩阵乘法的结合律！

