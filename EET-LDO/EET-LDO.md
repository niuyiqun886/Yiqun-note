## chapter2

### 2.0

一个简单的放大器 $A$  和反馈因子 $F$  

![](./assets/cc306f7a-c3a9-4427-b846-6b14f4ac67cd.png)

这个公式是开环的：

$$
A \approx (R_{o1} \parallel \frac{1}{s C_{p1}}) \cdot g_{m} \cdot (R_{o2} \parallel \frac{1}{s C_{p2}})-----(2-1)
$$

$$
F \approx SC_{m} -----(2-2)
$$


两个独立的极点 $\frac{1}{R_{o1}C_{p1}}$  和   $\frac{1}{R_{o2}C_{p2}}$  。
放大器是一个电压并联反馈放大器(shunt-shunt)，由简单的放大器A和反馈因子F组成，使用反馈定理表示从 $i_{s}$  到  $v_{0}$  的增益为：

$$
\begin{gather}
\frac{i_{s}}{v_{o}} \approx \frac{A}{1 + AF} & \approx \frac{1}{F} &&& when \;AF \gg 1 ----(2-3-1)  \\
\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \  & \approx A &&& when \;AF \ll 1 ----(2-3-2)
\end{gather}
$$


当 $C_{m}$  极度小，在米勒补偿前， $C_{m} \approx 0$  这时   $F \approx SC_{m}$  变得无穷小。导致了2-3-2的条件  $AF \ll 1$  ，所以极点和放大器A相同。


  $C_{m}$  足够小的时候，如下图，A的传递函数小于  $\frac{1}{F}$  ，极点就是A的开环极点 $p_{1}, p_{2}$  


![](./assets/e1eeb52b-ade4-4b2b-bed6-2fbbc61d04f8.png)


随着  $C_{m}$  逐步增加，AF也逐步增加，当  $C_{m}$  足够大的时候，$A F \gg 1$  的时候增益变为   $\frac{1}{F}$  。下图就是描述上述过程的。

![](./assets/f098a1e9-24d6-4265-bd87-3511577ed745.png)

极点的变化过程：
1. 低频段，式(2-1)化简为：

$$
A(s) = \frac{g_{m} R_{o1} R_{o2} }{(1 + s R_{o1} C_{p1}) (1 + s R_{o2} C_{p2})}
$$

两个独立的极点 $\frac{1}{R_{o1}C_{p1}}$  和   $\frac{1}{R_{o2}C_{p2}}$  ，直流增益为  $A_{0} = g_{m} R_{o1} R_{o2}$ 
图中蓝色虚线的模  $\left| \frac{1}{F} \right| = \frac{1}{(\omega C_{m})}$  
交点的条件： $\left| A F \right| = 1$  也就是两个曲线相等。

求解： 当   $C_{m}$  在增大时两个曲线出现交点，几点发生变化

$p_{1} \to p_{1}'$   令：

$$
A_{0} = g_{m} R_{o1} R_{o2} =  \left| \frac{1}{F} \right| = \frac{1}{(\omega C_{m})}
$$

解得：

$$
p_{1}' = \frac{1}{ g_{m} R_{o1} R_{o2} C_{m}}
$$


$p_{2} \to p_{2}'$   令


$$
\begin{gather}
\left| A(s) \right| = \frac{g_{m} R_{o1} R_{o2} }{(1 + s R_{o1} C_{p1}) (1 + s R_{o2} C_{p2})} \approx \frac{g_{m} R_{o1} R_{o2}}{\omega^{2} R_{o1} C_{p1} R_{o2} C_{p2}} = \frac{g_{m}}{\omega^{2} C_{p1} C_{p2}}\\
\left| A(s) \right| = \left| \frac{1}{F} \right| = \frac{1}{(\omega C_{m})}
\end{gather}
$$



解得：

$$
p_{2}' = \frac{g_{m} C_{m}}{C_{p1} C_{p2}}
$$

==**存在一个漂亮的不变量：**==

$$
p_{1}' \cdot p_{2}' =  \frac{1}{ g_{m} R_{o1} R_{o2} C_{m}} \cdot \frac{g_{m} C_{m}}{C_{p1} C_{p2}} = \frac{1}{R_{o1}C_{p1}} \cdot \frac{1}{R_{o2}C_{p2}} = p_{1} \cdot p_{2}
$$

2.1应用负反馈的方法去密勒补偿
没有其他提示的话：下面假设  $g_{mi} \gg 1; C_{mi},C_{L} \gg C_{pi}$  

![600](./assets/7be59180-952f-4f59-9bfd-8f7250356c82.png)

上图可以变为：模块H和反馈元件 $C_{m1}$  ，传递函数根据闭环增益等式表示为：

$$
\frac{V_{0}(s)}{V_{in}(s)} \approx A_{\infty} \cdot \frac{T}{1 + T} + \frac{d}{1 + T} \approx A_{\infty} \cdot \frac{T}{1 + T}
$$

T：密勒环路增益； 当 $T = \infty$ 时 $A_{\infty}$  是理想的闭环增益；d是 $T = 0$ 时的直接前馈
前一项反映了极点的影响，后一项的d反映了前馈的零点影响，极点准单数零点不准。
[^1]



$$
T = \frac{-g_{m} \cdot R_{L} \parallel \frac{1}{SC_{L}}}{\frac{1}{SC_{p1}} + R_{L} \parallel \frac{1}{SC_{L}}} \cdot R_{o1} \parallel \frac{1}{SC_{p1}}
$$










































































[^1]: 注：这个公式在另一本书中表示为：
	
	$$
	\frac{V_{0}(s)}{V_{in}(s)} \approx A_{\infty} \cdot \frac{T}{1 + T} + A_{0} \frac{1}{1 + T} \approx A_{\infty} \cdot \frac{T}{1 + T}
	$$
	
	其中 $A_{0}$  为输入为0的时候的增益，当让输入为0的时候输出也为0，所以  $A_{0} = 0$  也就可以得到上面的公式了。  
