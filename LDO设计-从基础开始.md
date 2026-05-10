# LDO 学习笔记
   
## Tutorial Roadmap(学习路线)

   - [ ] Performance metrics(性能指标)
   - [ ] Stability(稳定性)
   - [ ] Power supply rejection(电源抑制)
   - [ ] Summary
### Performance metrics
   - Dropout Voltage 
   - Quiescent Current
   - Efficiency
   - Line Regulation
   - Load Regulation
   - Line Transient Response
   - Load Transcient Response
   - Power Supply Rejection
   - Accuracy


我的设计指标：（Vin范围最大为3V）

![697](assets/设计指标.png)

claude code 算出的μpCox的值

![653](assets/claude_code算出的μpCox的值.png)

## 第一步pass管的设计
### 1.1核心约束

输出电压为1.8V，那么在Vds=200mV的时候，VDD=2V，给出管子的L为2倍的最小值，然后将管子的Vov固定为200mV，这样管子一定工作在饱和区，然后通过调整管子的W值将电流调整为最大需要的负载电流。


| 数值    | 约束       | 含义            |
| ----- | -------- | ------------- |
| 2.0V  | Vin_min  | dropout是的最低输入 |
| 1.8V  | Vout     | 输出标称          |
| 10mA  | Iout_max | 满载电流          |
| 200mV | VDO_max  | dropout压降上限   |


![393](assets/pass管子扫描.png)

仿真结果：

$$
\begin{gather}

VDD = 2V，V_{g} = 1.07V，V_{d} = 1.8V，μ_{p}\cdot C_{ox} = 60μA/V^{2} \\
V_{sg} = VDD - V_{g} = 2-1.07 = 0.93V \\
V_{ov} = V_{sg}-|V_{th}| ≈ 210mV \\
I_{d} = \frac{1}{2}×μ_{p}\cdot C_{ox}\frac{W}{L}\cdot V_{ov}^{2} = 0.5 × 60μ × 16800 × （0.21)^{2} ≈ 14.8mA
\end{gather}
$$

这个计算的结果和仿真的接近。最终去==W/L= 120×50u/0.5u==，电流大小为10.2559mA。

### 1.2测试
#### 测试1：VDO-Iload全扫描
仿真设置“


$$
\begin{gather}
DC\ sweep :V_{in}→from\ 3V\ to\ 1.5V \\
Parametric\ sweep:\ Iload = 0.1mA,\ 1mA,\ 5mA,\ 10mA,\ 12mA \\
可以将V_{g}设置为：\ V_{g} = V_{in} - 0.93V,\ 即跟随V_{in}保持恒定
\end{gather}
$$

仿真电路：

![365](assets/测试1.png)

仿真结果：

![697](assets/测试1仿真结果.png)

仿真结果：Vin=2V

| Iload | Vd     | Vsd=Vin-Vd | PMOS区域   |
| ----- | ------ | ---------- | -------- |
| 100u  | 1.997V | 3mV        | 深triode  |
| 1mA   | 1.989V | 11mV       | 深triode  |
| 5mA   | 1.943V | 57mV       | triode   |
| 10mA  | 1766V  | 234mV      | 饱和边界     |
| 12mA  | -1.96V | -          | PMOS无法供电 |

结论：
（1）在固定Vgs的情况下，12mA的时候曲线塌到-1.96V，这说明PMOS的**饱和电流上限大约在10~11mA之间**。一旦Iload超过这个值，电流sink要不到电流，节点电压被sink拉到compliance极限。
（2）在 Iload=10mA 时 Vsd=234mV，比目标的200mV偏大，这是由于沟道长度调制效应的影响，反推：

$$
I_{d} = \frac{1}{2}×μ_{p}\cdot C_{ox}\frac{W}{L}\cdot V_{ov}^{2}(1+λV_{sd})
$$
10mA带入：
$$
I_{d,sat}\cdot (1+λ\cdot 0.234) = 10mA 
$$
由仿真可知 λ ≈ 0.7787/V，那么 (1 + 0.7787×0.234) = 1.182，所以 Id_sat0 = 10/1.182 ≈ 8.46mA。
说明这里的Vgs提供不了这么大的电流，故需要增大管子的宽长比。
**增加宽长比再次仿真：得到Vds=201mV，故在输出电流为10mA，输出为1.8V是最低的输入电压为1.8V+0.201V=2.001V，满足最低的2V要求。**

==要在所有工艺角下都满足要求。==

![](assets/测试1仿真增加W：L.png)




这里的**Vsd = 234mV**叫做=="saturation headroom"（饱和余量）==PMOS要保持在饱和区所需的Vsd下限。
这个数字也很重要：
- 在saturation里，pass管的小信号参数（gm, rds）行为良好，环路特性可控。
- 一旦进入triode，gm会随Vds变化，rds急剧下降（变成Ron），整个环路动态特性会改变。
- LDO设计中通常希望正常调节状态下PMOS在饱和区，dropout只是边界条件。
所以这个234mV告诉你：只要 Vin > 2.034V，pass管就稳稳在饱和区。这是一个对环路设计有意义的spec。

#### 测试2






### 1.3小信号参数提取
