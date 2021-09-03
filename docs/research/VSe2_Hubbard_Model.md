---
layout: default
title:  基于Hubbard模型的VSe2能带模型建立与参数拟合
---

# 概述
 - 建立了适合于VSe2晶格的Hubbard模型，并给出了具体的表达式。
 - 代码化Hubbard模型的推导，将一般考虑到3阶的跃迁项扩展到任意阶。
 - 使用粒子群和拟牛顿算法拟合跃迁参数。

# 背景
在[VSe2的第一性原理研究](./VSe2_Ab_initio)中，DFT计算给出了VSe2的能带结构。DFT计算基于平面波近似，对于强关联系统，我们希望通过电子跃迁的角度来考虑磁性起源的问题，因此需要使用紧束缚模型(Tight-binding moddel)来考虑问题。紧束缚模型可以通过格点之间的跃迁，得到色散关系，即为能带结构。通过调节跃迁参数拟合DFT计算的能带结构，就可以得到具有实际物理意义的跃迁模型。Hubbard模型是紧束缚模型的一种表达方式，其形式为：

$$H = -\sum_{ijm\sigma}{t^m_{i}C_{im\sigma}^{\dagger}C_{jm\sigma}} + \sum_l (Un_{l1\uparrow}n_{l1\downarrow} + Un_{l2\uparrow}n_{l2\downarrow})$$
其中$$t_{i}$$是跃迁参数，$$C_{im\sigma}^{\dagger}, C_{jm\sigma}$$是粒子产生湮灭算符，代表了粒子在某一格点湮灭，在另一格点产生，即粒子在两个格点之间发生跃迁。对于不同的晶格，一般具有不同的晶格矢量和等效跃迁位置。例如所有晶体具有230种空间群，即具有此数量的对称性，手工建立适用于不同体系的Hubbard模型是不现实的。

# 模型建立
仅考虑Hubabrd模型的动能项，以最近邻跃迁为例：
<!-- <img src="NearstNeighbour.png" width="700"></img> -->
![](/image/NearstNeighbour.png)

$$\begin{cases}
1: & H_1^1 = t_1^m C_{\boldsymbol{l}m\sigma}^{\dagger}C_{(\boldsymbol{l}-\boldsymbol{a})m\sigma}\\
2: & H_1^2 = t_1^m C_{\boldsymbol{l}m\sigma}^{\dagger}C_{(\boldsymbol{l}-\boldsymbol{a}-\boldsymbol{b})m\sigma}\\
3: & H_1^3 = t_1^m C_{\boldsymbol{l}m\sigma}^{\dagger}C_{(\boldsymbol{l}-\boldsymbol{b})m\sigma}\\
4: & H_1^4 = t_1^m C_{\boldsymbol{l}m\sigma}^{\dagger}C_{(\boldsymbol{l}+\boldsymbol{a})m\sigma}\\
5: & H_1^5 = t_1^m C_{\boldsymbol{l}m\sigma}^{\dagger}C_{(\boldsymbol{l}+\boldsymbol{a}+\boldsymbol{b})m\sigma}\\
6: & H_1^6 = t_1^m C_{\boldsymbol{l}m\sigma}^{\dagger}C_{(\boldsymbol{l}+\boldsymbol{b})m\sigma}\\
\end{cases}$$

此处哈密顿量$$H_i^j$$代表从$$j^{th}$$向$$i^{th}$$近邻的跃迁。因此最近邻的动能项可以写为：

$$\begin{aligned}
    \varepsilon_1=&t_1^m[e^{ik_a}+e^{i(k_a+k_b)}+e^{ik_b}+e^{-ik_a}+e^{-i(k_a+k_b)}+e^{-ik_b}]\\
    =&2t_1^m[\cos{k_a}+\cos{k_b}+\cos{(k_a+k_b)}]
\end{aligned}$$

然而随着跃迁参数的增多，动能项包含的项数显著增多，你可以[在这里](./top20_Equation)看到前20项跃迁的表达式。因此我们选择利用coding的方式，给出直到第n项的Hubbard动能项系数。带入实际的晶格参数，就建立起了满足VSe2晶格的Hubbard模型。随机初始化跃迁参数，计算并拟合实际计算的能带结构，就可以得到具有物理意义的跃迁参数。

# 参数拟合
在所需要的拟合参数较少的情况下，基于线搜索的算法能够收敛到较好的解。而在搜索空间较大时，传统线搜索算法通常会掉入局域解。此时引入粒子群算法，可以尽可能的让搜索过程跳出局域解的吸引。在PSO进行到一定程度后，使用拟牛顿的线搜索算法进一步逼近全局最优，提高解的精度。

待拟合的系统在每个K格点具有能量值(E-k关系)，互相之间是独立的。加上PSO算法不同粒子之间的天然独立性，可以使用并行计算加速搜索的过程。

# 研究性质
硕士期间攻读课题，2020.09-至今。

# 技术栈
Matlab，并行计算。
* * *
### [back](/)