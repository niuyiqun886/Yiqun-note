## lecture1

→ A device efficiently converting current to voltage
→The very front-end in the RX of optical serial links(接收信号的最前端，光串联链路)
→ Provide reasonable gain（增益不会太大）introducing minimal noise and briding signal to the subsequent blocks.(引入最小的噪声，桥接信号和后面的模块)

Optical RX

![](./assets/6a4d4c05-15b8-4bad-aa41-c2cce0cba7c4.png)

光电二极管→TIA→(可能用到单端转双端，或者后面再做单端转双端)→再做CTLE+LA、或者equallazer(均衡器)这些


TIA：不能饱和(需要调控TIA的gain)，需要gain contral 越自动越好(最好是自己可以判断调整gain，在模拟世界解决)

阻抗要求：大多数情况是没什么阻抗的要求的，但是还是输入阻抗越小越好(保证感应的电流大多数都进入TIA)

介绍PD：Photo diode

![](./assets/230d582a-0a68-4da6-953f-3e1f52ad0fe4.png)![|347x233](./assets/c4da7f13-eb5f-422e-9116-3fcb1d8c04dc.png)

另一个名称：P-intrinsic-N(PIN)
特点：  
	1. 反偏
	2.光照进二极管耗尽区时产生电流

光照产生反向的反流，如右图，不同光照强度产生不同的电流。

判断PD的好坏参数：Responsivity如下图：

![](./assets/b5643b2b-2bc3-4e8f-a32a-5f81df01587b.png)

Responsivity(R)：会和偏压有关系
定义如下:（其中Input light Power 是光的功率可以量的出来）

$$
R \triangleq \frac{Induced \, I}{ Input \, light \, Power} (A/W)
$$

举例：
	1. R = 0.5 (A/W)    for    850nm lacer(激光)
	2. R = 0.9 (A/W)    for    1.55μm lacer

ER (Extinction Ratio)(消光比)：逻辑1时候的power和逻辑0时候的power（关断和开启）

$$
ER \triangleq \frac{P_{1}}{P_{0}} = \frac{Logical \, 1 \, Power}{Logical \, 0 \, Power} \, (A/W)
$$


## lecture2

Input-REF Noise

High-speed (broadband) devices →
Need to consider whole spectrum(of interest)

![](./assets/69ab4e9e-a674-4821-b4ae-5e94edb3aaf6.png)

输出等效噪声除增益 $R_{T}^2$   ；在带宽内积分，积分的结果才是所有的Noise

增益 $R_{T}^2$  也可能是和频率相关的。直接用  $R_{T,DC}$  来除就好了
输入噪声定义：

$$
\overline{I_{n,in}^2} \triangleq \frac{\int_{0}^{\infty} \overline{V_{n,out}^2} \,dx}{R_{T,DC}^2}
$$

RMS Noise current：

$$
I_{n,rms} = \sqrt{\overline{I_{n,in}^2}}
$$

TIA must contribute as little noise as possible to ensure sensitivity ：贡献尽可能少的噪声























































































