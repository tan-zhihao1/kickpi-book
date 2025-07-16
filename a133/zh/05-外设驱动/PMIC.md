# PMIC

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
```

电池配置，配置电池相关数据节点

```
battery_power_supply: battery-power-supply {
    compatible = "x-powers,axp803-battery-power-supply";
    status = "okay";
    ...
}
```



## 参考文档

```
Linux_PMIC_开发指南.pdf
```

