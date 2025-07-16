# PMIC

**dts**

PMIC 包含 regulator，power supply，power key。

PMU 的配置是通过设备树进行配置。

```
longan/device/config/chips/a133/configs/c3/kickpi-k5.dts
longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
```

pmu 配置

```
pmu0: pmu@34 {
    compatible = "x-powers,axp803";
    reg = <0x34>;
    status = "okay";
    ...
}
```



**battery dtsi**

```
longan/kernel/linux-4.9/arch/arm64/boot/dts/sunxi/a133-kickpi-axp707-bat-5000mA.dtsi
```







## 参考文档

```
Linux_PMIC_开发指南.pdf
```

