# PWM



### 普通PWM

例如：将K1拓展引脚上的GPIO0_C0（pwm1m0）配置成PWM

注意：任何IO都只能配置一种功能，如果要修改已经有使用的IO需要找到对应位置注释掉原本的使用

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi

+&pwm1 {
+    status = "okay";
+    pinctrl-0 = <&pwm1m0_pins>;
+};
```

测试方法参考:12-硬件功能测试 [PWM](12-硬件功能测试.md#PWM)

### 红外使用的PWM

永久关闭板子红外遥控，修改设备树目录下rk3568-kickpi-ir.dtsi文件

Android_SDK为例：

```diff
---kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-kickpi-ir.dtsi
+++kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-kickpi-ir.dtsi

&pwm7 {
-   status = "okay";
+   status = "disabled";
    compatible = "rockchip,remotectl-pwm";
```



