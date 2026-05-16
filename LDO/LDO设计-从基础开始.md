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


| 类别     | 参数              | 取值                                          | 说明                   |
| ------ | --------------- | ------------------------------------------- | -------------------- |
| 基本电压电流 | Vin范围           | 2V~3V                                       |                      |
|        | Vout            | 1.8V                                        |                      |
|        | Iout_max        | 10mA                                        |                      |
|        | Iout_min        | 0mA（空载）                                     | 必须空载稳定               |
| 静态指标   | VDO@Iout_max    | ≤200mV                                      | 最低Vin=2V时仍能稳压        |
|        | Iq@空载           | ≤50μA                                       |                      |
|        | 初始精度            | ±2%                                         | Vout在1.764V~1.863V之间 |
| DC调整率  | Line Regulation | ≤5mV/V                                      | DC Loop Gain ≥50dB   |
|        | Load Regulation | ≤5mV/10mA                                   | 闭环Rout≤0.5Ω          |
| 瞬态指标   | Load Transient  | ΔIout:1mA→10mA,edge=1μs <br>ΔVout < ±50mV   | 1μs是1mA跳到10mA所用的时间   |
|        | Line Transient  | ΔVin: 2.5V→3.3V, edge=1μs <br>ΔVout < ±30mV |                      |
| 频域指标   | PSRR @ 100Hz    | ≥ 60dB                                      | 由DC环路增益决定            |
|        | 相位裕度            | ≥ 60°                                       | 主极点补偿基本要求            |
|        | 增益裕度            | ≥ 10dB                                      |                      |
| 外部条件   | Cout            | 1μF                                         | 片外陶瓷电容               |
|        | ESR             | 10mΩ ~ 100mΩ                                | MLCC典型值              |
| 内部参考   | Vref            | 1.0V                                        | 假设有理想bandgap         |
| 环境     | 温度范围            | -40°C ~ 85°C                                |                      |
|        | 工艺              | TSMC 0.18μm                                 |                      |
|        | Corner          | TT为主，FF/SS验证                                |                      |


claude code 算出的μpCox的值

![653](LDO/assets/claude_code算出的μpCox的值.png)

## 第一步pass管的设计
### 1.1核心约束

输出电压为1.8V，那么在Vds=200mV的时候，VDD=2V，给出管子的L为2倍的最小值，然后将管子的Vov固定为200mV，这样管子一定工作在饱和区，然后通过调整管子的W值将电流调整为最大需要的负载电流。


| 数值    | 约束       | 含义            |
| ----- | -------- | ------------- |
| 2.0V  | Vin_min  | dropout是的最低输入 |
| 1.8V  | Vout     | 输出标称          |
| 10mA  | Iout_max | 满载电流          |
| 200mV | VDO_max  | dropout压降上限   |


![393](LDO/assets/pass管子扫描.png)

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

![320](LDO/assets/测试1.png)

仿真结果：

![697](LDO/assets/测试1仿真结果.png)

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

![531](LDO/assets/测试1仿真增加W：L.png)




这里的**Vsd = 234mV**叫做=="saturation headroom"（饱和余量）==PMOS要保持在饱和区所需的Vsd下限。
这个数字也很重要：
- 在saturation里，pass管的小信号参数（gm, rds）行为良好，环路特性可控。
- 一旦进入triode，gm会随Vds变化，rds急剧下降（变成Ron），整个环路动态特性会改变。
- LDO设计中通常希望正常调节状态下PMOS在饱和区，dropout只是边界条件。
所以这个234mV告诉你：只要 Vin > 2.034V，pass管就稳稳在饱和区。这是一个对环路设计有意义的spec。

#### 测试2

闭环测试，理想运放：增益100K，VDD= 2.5V，Iload=0:1mA：12mA

![](LDO/assets/闭环测试.png)

仿真结果：从仿真图中看出，负载为10mA时Vov为197mV，功率管在饱和区，满足设计要求。

![391](LDO/assets/闭环测试结果.png)


### 1.3小信号参数提取
测试电路：

![](LDO/assets/闭环测试.png)

测试条件：VDD去一个中间的点2.5V，其他如图。Iload= 10uA 1mA 10mA

==***PASS管的参数：***==

| Iload |   Vgs   |   Vov   |   gm   |  gds   |  rds   |   Cgs   |   Cgd   | A_pass |
| :---: | :-----: | :-----: | :----: | :----: | :----: | :-----: | :-----: | ------ |
| 10uA  | -503.3m | -217.9m | 248.1u | 670.5n | 1.491M | -2.002p | -1.928p | 32dB   |
|  1mA  | -729.9m |  8.6m   | 16.56m | 57.33u | 17.44k | -5.963p | -1.955p | 28.6dB |
| 10mA  | -918.2m |  197m   | 83.82m | 568.6u | 1.759k | -9.737p | -1.965p | 22.7dB |
#### ==三个问题：==
##### 1.轻载是PMOS在亚阈值区
Vov = -218 mV（负值）说明Vsg < |Vtp|，PMOS此时**没有强反型沟道**，靠扩散电流工作。
体现在数据里：
- gm 比满载小 **338倍**（248μA/V vs 83.8mA/V）
- rds 比满载大 **850倍**（1.49MΩ vs 1.76kΩ）

这**不是异常**，是LDO的**固有特性**——空载时pass管必然在亚阈值。
这意味着：
LDO的环路特性在不同负载下完全不同。后面做稳定性时，必须至少在 10μA、1mA、10mA 三个点都验证相位裕度，不能只看一个工作点。

##### 2.本征增益的变化

| Iload | gm*rds   |
| ----- | -------- |
| 10uA  | 370≈51dB |
| 1 mA  | 288≈49dB |
| 10mA  | 148≈43dB |
满载的时候本征增益反而最低，rds下降的比gm快。

含义：
- **Load Regulation的最差工作点在满载**（rds最小，环路抑制最弱）
- 后面验证 load reg 重点看 Iload=10mA 这条数据

##### 3.EA的输出摆幅要求
|Vgs|从503mV变化到918mV，变化了415mV
这意味着：EA的输出电压范围


| 工况                         | Vg需求                             |
| -------------------------- | -------------------------------- |
| VDD=3V，Iload=10uA          | Vg≈2.5V                          |
| VDD=2.5V，Iload=10uA        | Vg≈1.5V                          |
| VDD=2.5V，Iload=10mA        | Vg≈1.58V                         |
| VDD=2V，Iload=10mA（dropout） | Vg≈1.08V（PMOS可能进入triode，需要更低的Vg） |


故Vg 的摆幅在==1.0V到2.5V。==
这里求λ：和之前仿真的有区别[pass管子扫描](LDO/assets/pass管子扫描.png)

$$
λ = \frac{g_{ds}}{I_{d}} = \frac{568.6\mu}{10mA}≈0.057/V
$$

## 第二步误差放大器设计

### EA的参数要求：

| 参数        | 目标值               | 来源约束              |
| --------- | ----------------- | ----------------- |
| Vol（最低输出） | ≤ 1.08V @ VDD=2V  | 满载dropout         |
| Voh（最高输出） | ≥ 2.3V @ VDD=3V   | 空载关断PMOS          |
| DC增益 A_EA | ≥ 50dB（含margin）   | PSRR@100Hz ≥ 60dB |
| UGB       | ≥1MHz（PSRR@10k驱动） |                   |
| Rout_EA   | ≤ 13.6kΩ          | PM ≥ 60°          |
| Itail     | ≤ 25μA            | Iq ≤ 50μA         |
| 输入offset  | ≤ 20mV            | 初始精度 ±2%          |
| 容性负载能力    | Cgg ≈ 12pF（满载）    | Slew rate要求       |
注意：由于反馈电阻为1M和800K，故反馈节点为高阻节点约为444K，会和放大器的输入电容构成一个极点。
- Cfb=100fF：fp_fb ≈ 3.6MHz（OK，远超UGB）
- Cfb=500fF：fp_fb ≈ 720kHz（⚠️ 接近UGB，可能要管）
- Cfb=1pF：fp_fb ≈ 360kHz（💀 必须补偿）


#### 1.Vol和Voh的确定：

电源电压2V时满载，最低输出1.08V；电源电压3V时空载最高电压1.08V

#### 2.DC增益
**约束来源**：Line Reg ≤ 5mV/V → DC Loop Gain ≥ 50dB；Load Reg → Rout_closed ≤ 0.5Ω；PSRR@100Hz ≥ 60dB（最严苛）。

![](LDO/assets/LDO输出与环路增益关系.png)


==根据负载调整率和线性调整率：5mV/V；5mV/10mA==
##### 负载调整率：10mA处PMOS的rds=1.76kΩ，gm=83.8m，β=0.5556
Load Regulation spec：5mV/10mA → Rout_closed < 0.5Ω，由于负反馈，输出电阻被降低了环路增益倍：


$$
\begin{gather}
R_{out,close} =\frac{r_{dsp}}{g_{mp}\cdot r_{dsp}\cdot A_{EA}\cdot β} (当T\gg1) \\ 
A_{EA}> \frac{1}{g_{mp}\cdot \beta\cdot R_{out,close} }= \frac{1}{0.0838\times 0.5556\times 0.5} = 43 \approx 33dB
\end{gather}
$$

可知EA的增益只需要33dB就可以过Load Regulation 负载调整率。


##### 线性调整率：由图[LDO输出与环路增益关系](LDO/assets/LDO输出与环路增益关系.png)可知：PSRR，10mA负载

$$
\begin{gather}
V_{o} = V_{in}\cdot \frac{1}{A_{EA}\cdot g_{mp}\cdot r_{dsp}\cdot β}
\end{gather}
$$

**PSRR spec：60dB @ 100Hz**环路总DC增益 T = A_EA · gm · rds · β 需要至少 1000：

$$
\begin{gather}
A_{EA}> \frac{1000}{g_{mp}\cdot r_{ds}\cdot \beta }= \frac{1}{0.0838\times 0.5556\times 163} \approx 131 \approx 42.3dB
\end{gather}
$$


### 结论:
**两个结论去最大的，即42.3dB，EA的DC增益只要 50dB 就完全够用**——这给我们的EA拓扑选择留了很大空间。可以选简单的单级telescopic或folded cascode，不需要两级结构。

##### 3.UGB单位增益带宽

负载瞬态响应：ΔVout < 50mV @ ΔI=9mA/1μs（小信号近似）
$$
\begin{gather}
ΔV_{out} \approx \frac{ΔI}{2\cdot \pi UGB\cdot C_{out}} \\
UGB \geq \frac{9mA}{2\pi×1\mu F ×50mV} = 28.6kHz
\end{gather}
$$
Load  Trans 给出 UGB≥30kHz

线性瞬态响应：
ΔVin = 0.8V，1μs上升沿。在边沿期间，如果EA响应不够快，pass管的Vgate不跟随Vin，则Vsg会瞬时变化，引发大ID扰动：

$$
\Delta I_{out,peak} \approx \Delta V_{sg}\cdot g_{m,pass}
$$
要在UGB有限带宽下把这个扰动压在30mV内，需要loop响应时间快于：
$$
\begin{gather}
\tau_{loop} \leq \frac{\Delta V_{out}\cdot C_{}}{}
\end{gather}
$$





失调约束：
初始精度 ±2% × 1.8V = ±36mV。referred到EA输入：ΔVin_offset = ΔVout × β = 36mV × 0.556 = **20mV**。

5T OTA失调主要来自输入对Vth失配：σ(ΔVth) = AVT/√(WL)。TSMC 0.18μm的AVT ≈ 4-5mV·μm。要σ ≤ 7mV（3σ ≤ 21mV）：