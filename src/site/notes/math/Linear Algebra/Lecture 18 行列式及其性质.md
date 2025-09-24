---
{"created":"2025-09-24T10:16","updated":"2025-09-24T12:36","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 18 行列式及其性质/","dgPassFrontmatter":true,"noteIcon":""}
---

### Overview
从这个Lecture开始，开始专门聚焦于方阵，矩阵中有两个大的话题，行列式和特征值
需要行列式的重要原因: 求特征值
行列式 是一个 与方阵有关的数，这一个数可以告诉我们矩阵什么样子，比如行列式告诉了我们矩阵的可逆性
行列式的表示，det A 或者$|A|$
### 三个行列式的基础性质
教授首先介绍了行列式的三个基本性质，这三个性质定义了行列式是什么
**性质1: 对于单位矩阵I，有det I=1.**
**性质2:  交换行，行列式的值的符号会相反**
从性质一和性质二实际上就能得到所有的置换矩阵的行列式，值为1或-1
**性质3.a: 行列式按行提取矩阵中的系数**
$\begin{vmatrix}ta&tb\\c&d\end{vmatrix}=t\begin{vmatrix}a&b\\c&d\end{vmatrix}$
**性质3.b: 行列式是一个线性函数，但是线性反映在每一行上**
${\begin{vmatrix}a+a’& b+b’\\c& d\end{vmatrix}=\begin{vmatrix}a& b\\c& d\end{vmatrix}+\begin{vmatrix}a’& b’\\c& d\end{vmatrix}}$
其实有了性质3，我感觉就能构造出所有的矩阵的行列式?

### 推导出的性质
行列式的其他性质都可以由上面的三条基本性质推导出来
**性质4: 如果两行相等，那么行列式等于0**
可以用行列式2证明，交换相等的两行，一方面矩阵完全不变，一方面行列式会取反，所以这个数只能是0
**性质5: 从k行中减去第i行的l倍，行列式不变**
$$
\begin{vmatrix}a& b\\c-la& d-lb\end{vmatrix}\stackrel{3.b}{=}\begin{vmatrix}a& b\\c& d\end{vmatrix}+\begin{vmatrix}a& b\\ -la& -lb\end{vmatrix}\stackrel{3.a}{=}\begin{vmatrix}a& b\\c& d\end{vmatrix}-l\begin{vmatrix}a& b\\a& b\end{vmatrix}\stackrel{4}{=}\begin{vmatrix}a& b\\c& d\end{vmatrix}
$$
这个动作实际上是我们在做消元中做的基础行变换的动作，这个性质也就是说，做基础行变换实际上并不会改变矩阵的行列式
**性质6: 如果方阵的某一行全为0，那么其行列式值也为0**
$\begin{vmatrix}0&0\\c&d\end{vmatrix}=\begin{vmatrix}t\cdot0&t\cdot0\\c&d\end{vmatrix}=t\begin{vmatrix}0&0\\c&d\end{vmatrix}$ 
这说明确实可以用行列式才判断是否可逆，这是我们消元完可能出现的情况，即一行全为0，而它的行列式为0，也就是说，行列式实际上可以与秩，可逆挂钩
**性质7: 上三角矩阵的对应行列式的值等于其对角线上元素的乘积**
如果有上三角矩阵U，其行列式为$U=\left|\begin{array}{cccc}{d_{1}} & {*} & {\cdots} & {*} \\ {0} & {d_{2}} & {\cdots} & {*} \\ {\vdots} & {\vdots} & {\ddots} & {\vdots} \\ {0} & {0} & {\cdots} & {d_{n}}\end{array}\right|$,则$\det U=d_1d_2\cdot d_n$
证明：用性质5将最后一行开始，将对角元素上方的 * 元素依次变为0，$D=\begin{vmatrix}d_{1}& 0& \cdots& 0\\0& d_{2}& \cdots& 0\\\vdots& \vdots& \ddots& \vdots\\0& 0&\cdots&d_{n}\end{vmatrix}$的对角行矩阵，在使用性质3得到$d_nd_{n-1}\cdots d_1\begin{vmatrix}1& 0& \cdots& 0\\0& 1& \cdots& 0\\\vdots& \vdots& \ddots& \vdots\\0& 0& \cdots& 1\end{vmatrix}$,然后使用性质1

从矩阵A到U的过程中，可能会出现换行，矩阵A的真正的行列式可能不与U的行列式同号，这是由性质2决定的
**性质8: 当且仅当矩阵A为奇异矩阵时，det A=0, 当且仅当A不可逆时，det A=0**
首先由性质7可以知道，如果A为非奇异矩阵或者可逆的时候，在化成行阶梯形矩阵的时候，每一列都存在主元，所以对角线中实际上不会存在0元素
**性质9: detAB = detA detB**
行列式并不具备线性性质，也不具备加法性质，但是具备乘法性质
通过性质9，实际上能推出**逆矩阵的行列式公式**
$AA^{-1}=I \to |AA^{-1}|=1 \to |A||A^{-1}|=1 \to |A^{-1}|=\frac{1}{|A|}$
也可以推出$|A^2| = |A|^2$
但是要注意 $det(2A) = 2^ndet(A)$，2A实际上是把A中的每一个元素都乘以2，一行一行往外提，需要提n行

**性质10: $detA^T = detA$**
前面一直在关注行的属性给行列式带来的变化，有了这条性质，行的属性同样适用于列 -- 行和列是等效的
证明，将矩阵化为LU形式有$A^T=U^TL^T,A=LU$,那么$|A^T|=|U^TL^T|=|U^T||L^T|, |A|=|LU|=|L||U|$ L和U都是三角啊，都是对角线



### 行列式: 超体积
行列式的几何意义是向量所张成物体的一种超体积的测度
对于一个 2x2 矩阵，行列式是两个向量张成的平行四边形的**面积**
对于一个 3x3 矩阵，行列式是三个向量张成的平行六面体的**体积**
