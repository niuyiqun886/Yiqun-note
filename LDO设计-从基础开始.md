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

![](设计指标.png)

## 第一步pass管的设计
### 1.1核心约束

输出电压为1.8V，那么在Vds=200mV的时候，VDD=2V，给出管子的L为2倍的最小值，然后将管子的Vov固定为200mV，这样管子一定工作在饱和区，然后通过调整管子的W值将电流调整为最大需要的负载电流。

![393](assets/pass管子扫描.png)

仿真结果：

$$

$$

| 约束       | 数值    | 含义            |
| -------- | ----- | ------------- |
| Vin_min  | 2.0V  | dropout是的最低输入 |
| Vout     | 1.8V  | 输出标称          |
| Iout_max | 10mA  | 满载电流          |
| VDO_max  | 200mV | dropout压降上限   |
