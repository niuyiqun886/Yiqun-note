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

![697](设计指标.png)

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
\begin{align}

VDD &= 2V，V_{g} = 1.07V，V_{d} = 1.8V，μ_{p}\cdot C_{ox} = 60μA/V^{2} \\
V_{sg} &= VDD - V_{g} = 2-1.07 = 0.93V \\
V_{ov} &= V_{sg}-|V_{th}| ≈ 210mV \\
I_{d} &= \frac{1}{2}×μ_{p}\cdot C_{ox}\frac{W}{L}\cdot V_{ov}^{2} = 0.5 × 60μ × 16800 × （0.21)^{2} ≈ 14.8mA
\end{align}
$$

这个计算的结果和仿真的接近。最终去W/L= 120×50u/0.5u，电流大小为10.2559mA。

### 1.2测试
#### 测试1：VDO-Iload全扫描
仿真设置“

$$
\begin{align}
DC_{sweep} :V_{in}→from
\end{align}
$$
