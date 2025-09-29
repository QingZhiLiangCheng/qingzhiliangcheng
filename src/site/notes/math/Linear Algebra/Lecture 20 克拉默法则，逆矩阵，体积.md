---
{"created":"2025-09-29T14:50","updated":"2025-09-29T16:44","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 20 克拉默法则，逆矩阵，体积/","dgPassFrontmatter":true,"noteIcon":""}
---

### 逆矩阵公式
之前学过高斯若当法 就是通过$AI \to IA^{-1}$
今天所讲的是一种行列式的方法
$$
\displaylines{
A^{-1}=\frac{1}{\det (A)}C^T
}
$$
其中$C^T$是由代数余子式组成的，是代数余子式矩阵的转置，这个矩阵就是之前在课本上学的伴随矩阵啊，当时学的公式是
$$A^{-1}=\frac{A^*}{|A|}$$
要想证明这个式子成立，实际上是证明$AA^{-1}=I$即
$$
AC^T=det(A)I
$$
$$
\displaylines{
\begin{bmatrix}a_{11}& a_{12}& \cdots& a_{1n}\\\vdots& \vdots& \ddots& \vdots\\a_{n1}& a_{n2}& \cdots& a_{nn}\end{bmatrix}\begin{bmatrix}C_{11}& \cdots& C_{n1}\\C_{12}& \cdots& C_{n2}\\\vdots& \ddots& \vdots\\C_{1n}& \cdots& C_{nn}\end{bmatrix}=\overbrace{\begin{bmatrix}det(A)&0&\cdots&0\\0&det(A)&\cdots&0\\\vdots&\vdots &\ddots&\vdots\\0&0&\cdots&det(A)\end{bmatrix}}^{D}
}
$$

$AC^T$计算结果的第一个元素的结果实际上就是A的第一行和$C^T$的第一列的点乘，即$a_{11} * C_{11}+a_{12}*C_{12}+...+a_{1n}*C_{1n}$ , 关键是这个式子不正是A矩阵求行列式的时候的按行展开吗，所以说计算结果的第一个矩阵的值为det(A)
同理，只要是对角线上的值都是det(A)
而其他地方的值都是0，这是因为比如$a_{22} * C_{11}+a_{22}*C_{12}+...+a_{2n}*C_{1n}$如果按照按行展开的样子来看的话，实际上他的原行列式应该为类似这样子
$$
a_{21}(\begin{vmatrix} a_{22}& a_{23}\\a_{32}& a_{33}\end{vmatrix})+a_{22}(-\begin{vmatrix} a_{21}& a_{23}\\a_{31}& a_{33}\end{vmatrix})+a_{23}(\begin{vmatrix} a_{21}& a_{22}\\a_{31}& a_{32}\end{vmatrix})=
\begin{vmatrix}a_{21}&a_{22}&a_{23}\\a_{21}&a_{22}&a_{23}\\a_{31}&a_{32}&a_{33}\\\end{vmatrix}
$$
换句话说，他们的前两行是相同的，所以说行列式为0
有了逆矩阵公式，我们能够清楚地看到，当改变原矩阵中的一个元素时，会给逆矩阵带来什么样的变化！

### 克拉默法则
有了逆矩阵公式后，对Ax=b现在就有
$$
\displaylines{
x=A^{-1}b=\frac{1}{\det (A)}C^Tb
}
$$
更进一步，如果我们思考$C^T$b是什么的话，对于$C^Tb$结果中的每一个元素，都是一行点乘一列的结果，而这每一个一行点乘一列就是对于行列式按列展开的一个结果
所以有了克拉默法则
$$x_i = \frac{det(B_i)}{det(A)}$$
$$
\displaylines{
B_1=\begin{bmatrix}b_1& a_{12}& \cdots& a_{1n}\\b_2& a_{22}& \cdots& a_{2n}\\\vdots& \vdots& \ddots& \vdots\\b_n& a_{n2}& \cdots& a_{nn}\end{bmatrix}
}
$$
实际上就是换掉对应的列

克拉默法则很漂亮，但并不实用hhhh，计算量太大了

### 行列式的几何意义: 体积
行列式的几何意义实际上是一种测度，是向量所魏恒的那个东西的测度
对于二阶行列式来说就是平行四边形的体积，对于三阶行列式来说就是平行六面体的体积...
![Pasted image 20250929162432.png|500](/img/user/accessory/Pasted%20image%2020250929162432.png)
事实上，知道了几何意义，对于行列式的性质就有了一个集合的理解
- 交换两列，向量还是那些向量
- 行倍数
- 行加减
- 两行相同 行列式为0

