## chapter2

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


