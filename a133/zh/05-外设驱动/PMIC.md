# PMIC



## 相关术语说明

PMU 电源管理单元。
AXP 全志PMU的名称，如AXP152、AXP22x等。
LDO 是 lowdropout regulator，意为低压差线性稳压器。线性稳压器使用在其线性区域内运行的晶体管或FET，从应用的输入电压中减去超
额的电压，产生经过调节的输出电压。
DC-DC 是直流变直流，即不同直流电源值之间的转换，只要符合这个定义都可以叫DC-DC转换器，也包括LDO。但是一般的说法是把直流变直
流由开关方式实现的器件叫DCDC。
Regulator linux内核对LDO、DC-DC 的控制核心。







## 参考文档

```
Linux_PMIC_开发指南.pdf
```

