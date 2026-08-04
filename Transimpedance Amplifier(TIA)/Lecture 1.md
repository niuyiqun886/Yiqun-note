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

$$
R = 
$$

