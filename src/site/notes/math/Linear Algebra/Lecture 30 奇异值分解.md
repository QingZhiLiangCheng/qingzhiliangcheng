---
{"created":"2025-10-22T10:00","updated":"2025-10-22T21:00","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 30 奇异值分解/","dgPassFrontmatter":true,"noteIcon":""}
---

### 奇异值分解Overview
奇异值分解(singular value decomposiiton, SVD), 是将矩阵分解的一种方式
$$
A=U\Sigma V^T
$$
其中A是任意矩阵，$\Sigma$是对角矩阵，U和V是正交矩阵
结合[[math/Linear Algebra/Lecture 26 对称矩阵及其正定性\|Lecture 26 对称矩阵及其正定性]]所学的内容，如果矩阵A是一个对称矩阵，可以分解为$A=Q\Lambda Q^T$（谱定理），其中Q是正交矩阵，$\Lambda$是特征值矩阵
该分解形式是奇异值分解$A=U\Sigma V^T$的特殊情况，即矩阵U和矩阵V都等于Q(特征向量矩阵，而且是正交矩阵)
另外，奇异值分解和矩阵对角化$A=S\Lambda S^{-1}$是对矩阵分解的两种形式，虽然当矩阵A是对称矩阵时两者是相同的, 但是对于一般形式则不同：
- 奇异值分解适用于任意的矩阵A，而矩阵对角化只适用于具有n个线性独立的特征向量的矩阵；
- 奇异值分解所得到的U和V是正交矩阵；而矩阵对角化得到的矩阵S是以矩阵A的特征向量作为列向量所构成的矩阵，不一定是正交矩阵

### 奇异值分解的目的
![Pasted image 20251022104500.png|400](/img/user/accessory/Pasted%20image%2020251022104500.png)
在A的行空间中，存在标准正交基$v_1,v_2,...,v_r$
之前[[math/Linear Algebra/线性代数的本质：矩阵与线性变换\|线性代数的本质：矩阵与线性变换]]学过，矩阵A就是一种线性变换，它所蕴含的信息实际上就是一种变换完的一种基(列向量)，按照教授所教的子空间来看，实际上这个变换完的基实际上就是A的列空间
所以，对于一个向量$v_1$,$Av_1$就是A的列空间中的一个向量，如果把他变成单位向量就是$\sigma u_1=Av_1$。由于之前我们学过[[math/Linear Algebra/Lecture 17 正交矩阵，Schmidt正交化#Gram-Schmidt 正交化\|Schmidt正交化]]，所以事实上我们能通过Schmidt在A的行空间中找到一组正交基，能够做到左乘A变换到A的列空间
$$
A\begin{bmatrix} | & | & & | \\ v_1 & v_2 & \cdots & v_r \\ | & | & & | \end{bmatrix}=\begin{bmatrix} | & | & & | \\ u_1 & u_2 & \cdots & u_r \\ | & | & & | \end{bmatrix}
\begin{bmatrix}\sigma_1 &&&\\&\sigma_2\\&&\ddots\end{bmatrix}
$$
即
$$
\begin{aligned}AV&=U\Sigma \\
AVV^{-1}&=U\Sigma V^{-1}\\
A&=U\Sigma V^{-1}\\
\end{aligned}
$$
V是由正交基构成的，所以$V^{-1}=V^T$
即
$$
A=U\Lambda V^T
$$
这就是矩阵的奇异值分解
实现奇异矩阵的分解的关键是找到一组在行空间里的一组正交基, 通过矩阵A的线性变换后，所得到的在列空间中的一系列向量刚好也是正交的

对于另外两个相应的空间，零空间和左零空间，奇异值分解仍然适用
当矩阵$A_{m\times n}$行不满秩时，则行空间的正交基数量$r<n$（小于$R^n$的维度），相应的零空间的正交基数就是n-r, 如果将两个空间的正交基组合在一起，构成了一个n by n的矩阵$V'=\begin{bmatrix} | & | & & |& \textcolor{red}{|} & & \textcolor{red}{|} \\ v_1 & v_2 & \cdots & v_r &\textcolor{red}{v'_{r+1}} & \cdots & \textcolor{red}{v'_n} & \\ | & | & & | &| && \textcolor{red}{|}\end{bmatrix}$
列空间中正交基数量也是r，相应地左零空间的正交数量是m-r,将这两个空间的正交基组合在一起，构成一个m by m的矩阵$U'=\begin{bmatrix} | & | & & |& \textcolor{red}{|} & & \textcolor{red}{|} \\ u_1 & u_2 & \cdots & u_m &\textcolor{red}{u'_{m+1}} & \cdots & \textcolor{red}{u'_m} & \\ | & | & & | &| && \textcolor{red}{|}\end{bmatrix}$
要让添加了零空间(或左零空间)正交基的矩阵，满足奇异值分解公式$AV'=U'\Sigma$,只需要调整$\Sigma$的相应元素即可
由于零空间的向量x满足等式$Ax=0$,对于左零空间也类似，所以将矩阵$\Sigma_{m\times n}$相应对角线上的元素设置为0即可
$$
\begin{aligned}
A\begin{bmatrix} | & | & & |& \textcolor{red}{|} & & \textcolor{red}{|} \\ v_1 & v_2 & \cdots & v_r &\textcolor{red}{v'_{r+1}} & \cdots & \textcolor{red}{v'_n} & \\ | & | & & | &| && \textcolor{red}{|}\end{bmatrix}=\\\begin{bmatrix} | & | & & |& \textcolor{red}{|} & & \textcolor{red}{|} \\ u_1 & u_2 & \cdots & u_m &\textcolor{red}{u'_{m+1}} & \cdots & \textcolor{red}{u'_m} & \\ | & | & & | &| && \textcolor{red}{|}\end{bmatrix}
\begin{bmatrix}\sigma_1 &0&\cdots&0&0&0&0\\0&\sigma_2&\cdots&0&0&0&0\\\vdots&\vdots&\ddots&\vdots&\vdots&\vdots&\vdots\\0&0&\cdots&\sigma_r&0&0&0\\
0&0&\cdots&0&\textcolor{red}{0}&0&0\\\vdots&\vdots&\vdots&\vdots&\vdots&\ddots&\vdots\\
0&0&\cdots&0&0&0&\textcolor{red}{0}
\end{bmatrix}
\end{aligned}
$$

### 奇异值分解的步骤
对于矩阵$A=\begin{bmatrix}4&4\\-3&3\end{bmatrix}$,rank=2,为了奇异值分解要在行空间中找一组正交基$v_1,v_2$,列空间中找一组正交基$u_1,u_2$ ,以及一组系数$\sigma_1,\sigma_2$,使得下面的等式成立
$$
\left\{
\begin{aligned}
Av_1=\sigma_1u_1\\
Av_2=\sigma_2u_2
\end{aligned}
\right.
$$
$$
\begin{aligned}
A&=U\Sigma V^{T}\\
A^TA&=(U\Sigma V^{T})^TU\Sigma V^{T}= (V^{T})^T\Sigma^TU^TU\Sigma V^{T}\\
\end{aligned}
$$
U是正交矩阵，所以满足$U^T=U^{-1}$,即
$$
A^TA=V\Sigma^T\Sigma V^T
$$
这样就消去了U，而且$\Sigma$由于是对角矩阵，所以$\Sigma^T=\Sigma$
即
$$
A^TA=V\Sigma^2V^T=V\begin{bmatrix}\sigma^2_1&\\&\sigma^2_2\end{bmatrix}V^{T}
$$
而$A^TA$是个对称矩阵，所以这实际上就是一个正交化$Q\Lambda Q^T$的过程！
也就说我们要求$A^TA$的特征值，特征向量
$$
A^TA=\begin{bmatrix}4&-3\\4&3\end{bmatrix}\begin{bmatrix}4&4\\-3&3\end{bmatrix}=\begin{bmatrix}25&7\\7&25\end{bmatrix}
$$
$\lambda_1=32, \lambda_2=18$,特征向量为$\begin{bmatrix}1\\1\end{bmatrix},\begin{bmatrix}-1\\1\end{bmatrix}$,标准化操作得到：$\begin{bmatrix}\frac{1}{\sqrt 2}\\\frac{1}{\sqrt2}\end{bmatrix},\begin{bmatrix}\frac{1}{\sqrt 2}\\-\frac{1}{\sqrt2}\end{bmatrix}$
带回
$$
A=U\begin{bmatrix}\sqrt{32}&0\\0&\sqrt{18}\end{bmatrix}\begin{bmatrix}\frac{1}{\sqrt 2}&\frac{1}{\sqrt2}\\-\frac{1}{\sqrt 2}&\frac{1}{\sqrt2}\end{bmatrix}
$$
其实这样就可以解除U来了，当然也可以通过$AA^T=U\Sigma U^T$来求
$$
AA^T=\begin{bmatrix}4&4\\-3&3\end{bmatrix}\begin{bmatrix}4&-3\\4&3\end{bmatrix}=\begin{bmatrix}32&0\\0&18\end{bmatrix}
$$
特征值还是32,18 特征向量为$\begin{bmatrix}1\\0\end{bmatrix},\begin{bmatrix}0\\1\end{bmatrix}$
$$
A=U\Sigma V^T=\begin{bmatrix}1&0\\0&-1\end{bmatrix}\begin{bmatrix}\sqrt{32}&0\\0&\sqrt{18}\end{bmatrix}\begin{bmatrix}\frac{1}{\sqrt 2}&\frac{1}{\sqrt2}\\-\frac{1}{\sqrt 2}&\frac{1}{\sqrt2}\end{bmatrix}
$$


### 奇异矩阵的奇异值分解
对于不可逆(奇异)矩阵，U和V会包含零空间和左零空间的标准正交基，在矩阵$\Sigma$的对角线中的相应元素为0
对$A=\begin{bmatrix}4&3\\8&6\end{bmatrix}$进行奇异值分解
A的行空间只有$\begin{bmatrix}4\\3\end{bmatrix}$向量张成，列空间只有$\begin{bmatrix}4\\8\end{bmatrix}$向量张成
对于行空间和列空间很方便求出这些空间的正交基，实际上就只是一个单位化
$\begin{bmatrix}4\\3\end{bmatrix}=\begin{bmatrix}\frac{4}{5}\\\frac{3}{5}\end{bmatrix}$,$\begin{bmatrix}4\\8\end{bmatrix}=\begin{bmatrix}\frac{1}{\sqrt 5}\\ \frac{2}{\sqrt{5}}\end{bmatrix}$
由于零空间与行空间正交，左零空间与列空间正交，所以很容易求出零空间和左零空间的正交基，分别为$\begin{bmatrix}\frac{3}{5}\\-\frac{4}{5}\end{bmatrix},\begin{bmatrix} \frac{2}{\sqrt{5}}\\-\frac{1}{\sqrt 5}\end{bmatrix}$
$$
A^TA=\begin{bmatrix}4&8\\3&6\end{bmatrix}\begin{bmatrix}4&3\\5&6\end{bmatrix}=\begin{bmatrix}80&60\\60&45\end{bmatrix}
$$
rank=1，其中一个特征值为0，另一个为125，得$\Sigma=\begin{bmatrix}\sqrt{125}&0\\0&0\end{bmatrix}$
$$
A=\begin{bmatrix}\frac{1}{\sqrt5}&\frac{2}{\sqrt5}\\\frac{2}{\sqrt5}&-\frac{1}{\sqrt5}\end{bmatrix}\begin{bmatrix}\sqrt{125}&0\\0&0\end{bmatrix}\begin{bmatrix}\frac{4}{5}&\frac{3}{5}\\\frac{3}{5}&-\frac{4}{5}\end{bmatrix}
$$
