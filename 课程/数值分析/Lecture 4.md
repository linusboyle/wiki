# 线性方程组的迭代解法

## 基本概念

使用向量序列逼近准确解，类似于不动点迭代法，构造$x=\phi(x)$。（不能用L3中的牛顿法，见[L3](Lecture%203.md)）

希望$\phi$是线性的，则有 $x^{k+1}=Bx^k+f$

其中B和f是常数矩阵和向量，这称为**一阶定常迭代法**（迭代步只涉及前一步的结果，B和f是常数系数）

构造方法：**矩阵分裂法**

选择矩阵M，N使得A=M-N，则有

![](_v_images/20200325111436599_242604954.png)


## 迭代法相关理论

### 收敛性

一阶定常迭代法收敛，需要B的幂序列$lim_{k->\infty}B^k=0$

1. 矩阵序列收敛，等价于误差的范数收敛于0
2. 
![](_v_images/20200325114339287_1181886458.png)

3. 矩阵的谱半径为：
![](_v_images/20200325114310180_1848414834.png)

4. 
![](_v_images/20200325114706844_790647916.png)
（利用定理4.3，以及特征值的定义推导出）

**迭代法基本定理**：
![](_v_images/20200325114838176_286178752.png)
（或者参考[Lecture 3.md](Lecture%203.md)最后的定理）

因此，谱半径小于1是一阶定常迭代法的收敛判据。（全局收敛）

另外，收敛的一个**充分**条件是，在某种范数下$||B||_t<1$.

### 收敛阶

**收敛阶**定义类似：

![](_v_images/20200325115552417_1519720288.png)

可以证明，一阶定常迭代法是一阶收敛的，且常数c为B的谱半径。

### 收敛速度

![](_v_images/20200325115650639_376640528.png)

R反映了一步迭代取得的十进制精度位数

## Jacobi迭代法

如果A的**对角元不为0**，设A=D-(D-A)，其中D是A的对角阵。具体的迭代式表示如下：

- 三阶的情况
![](_v_images/20200325111736955_886831905.png)
- 更一般地
![](_v_images/20200325111847012_323866957.png)

### 判停准则

类似非线性方程求根，可以用残差判据和误差判据。

- 残差的计算量较大
- 误差判据较为常用

## Gauss-Seidel迭代法

和Jacobi类似，但是计算顺序不同！

![](_v_images/20200325115816817_218371586.png)

其矩阵表达式是A=L-(L-A)

![](_v_images/20200325120118363_1165088330.png)

- 如果按从n到1的顺序计算分量，则得到逆向GS算法
- 如果同时使用顺序和逆向的GS算法，则得到对称GS（SGS）算法

## SOR迭代法

在GS基础上引入松弛因子ω

先按照GS法由xk算出x'k+1，然后将xk和x'k+1加权平均得到xk+1

![](_v_images/20200325120649137_1677029794.png)

如果ω=1时就是GS方法

对应于分裂法中M=$\frac 1 \omega D - \hat L$的情况

## 三种迭代法的收敛条件

直接根据定义，计算B的谱半径往往计算量过大（Jacobi迭代法B较简单，可以直接算）

对于**Jacobi迭代法**，有如下判据：
- 如果B的1范数或无穷范数严格小于1，则Jacobi迭代法收敛
- 如果A是对角元大于0的对称阵，则迭代法全局收敛的充要条件是A和2D-A都正定

**G-S**迭代法收敛性的判断：
- 如果Jacobi方法的迭代矩阵B满足B的1范数或无穷范数严格小于1，则GS方法也收敛
- 如果A对称正定，则GS法收敛

**SOR**：
- SOR收敛的必要条件是0<w<2
- 如果A对称正定，则满足上一条件时SOR收敛

还有一些根据系数矩阵A直接判断的方法：

### 可约矩阵

如果存在排列阵P使得：

![](_v_images/20200401104216422_89779276.png)

则A是**可约矩阵**

### 对角占优定理

如果A是严格对角占优矩阵，或者**不可约**的弱对角占优矩阵，则A非奇异。

对于这样的A，求解Ax=b的Jacobi和GS迭代法都收敛；如果0<w<=1,则SOR迭代法也收敛。

## Krylov子空间迭代法

如果A是一个对称正定矩阵，则Ax=b的解也就是n元二次函数$\phi(x)=\frac 1 2 x^T A x - b^T x$的最小值点。 因此，可以转化为最小值点搜索问题。 因为A是对称正定的，\phi一定是凸二次函数，且有惟一最小值点。

搜索过程是，每一步选择一个搜索的方向以及搜索的步长，使得取到沿着这个搜索方向的多元二次函数的最小值。如果搜索方向已经确定是pk，则有一个惟一的步长取到此最小值：

$$
\alpha_k = \frac {r_k^T p_k} {r_k^T A p_k}
$$

其中，rk是该步的残差$r_k = b - A x_k$

选择搜索方向有不同的方法：

### 最速下降法

最速下降法每一步沿着下降最快的方向进行搜索。 下降最快的方向，也就是负梯度方向。 可以证明，负梯度方向等于残差$r_k$。因此pk=rk。

此时：

$$
\alpha_k = \frac {(r_k)^T r_k} {(r_k)^T A r_k}
$$

所以迭代公式为$x^{k+1} = x^k + \alpha_k r^k$

![](_v_images/20200401111805129_113432150.png)

因为最小值惟一，算法一定收敛。

最速下降法的缺点：
- 局部最优，但总体效率低
- 收敛慢

### 共轭梯度法

第一步用负梯度方向。之后，在当前点下降最快的方向是残差rk，假设前一步的搜索方向是pk-1，则在过xk和上述两个方向张成的平面上找函数的最小值，pk的计算式是：

$$
p_k = r_k + \beta_{k-1}p_{k-1}
$$

其中$\beta$的计算式是：

$$
\beta_{k-1} = - \frac {r_k^T A p_{k-1}} {p_{k-1}^T A p_{k-1}}
$$

而步长的计算公式带公式就可以了。

如果不考虑数值误差，n步迭代后得到准确解。但是，当k很大时，舍入误差严重；当A是病态矩阵，则收敛很慢，甚至不收敛