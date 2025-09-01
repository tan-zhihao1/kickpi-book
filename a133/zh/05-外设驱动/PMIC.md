# PMIC



## 参考文档

```
Linux_PMIC_开发指南.pdf
AXP707_Datasheet_V1.0.pdf
```



## DTS

**dts**

通过设备树进行配置 PMU。

```
longan/device/config/chips/a133/configs/c3/kickpi-k5.dts
longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
```

pmu 配置节点

```
pmu0: pmu@34 {
    compatible = "x-powers,axp803";
    reg = <0x34>;
    status = "okay";
    ...
}
```



**battery dtsi**

电池参考配置

```
longan/kernel/linux-4.9/arch/arm64/boot/dts/sunxi/a133-kickpi-axp707-bat-5000mA.dtsi
longan/kernel/linux-4.9/arch/arm64/boot/dts/sunxi/a133-kickpi-axp707-bat-8000mA.dtsi
```

电池配置，配置电池相关数据节点

```
battery_power_supply: battery-power-supply {
    compatible = "x-powers,axp803-battery-power-supply";
    status = "okay";
    ...
}
```



## 常见问题

### 充电电流偏低问题

问题一：VBUS 电压是否低于4.6V，电压偏低，触发限流

问题二：VBUS 串接的电流计存在较大压降（超过0.2V）

问题三：USB 线损和板级OVP压降，导致 VBUS 电压偏低

更换低阻抗的USB电流计测试；

更换线损较小的USB线材；



