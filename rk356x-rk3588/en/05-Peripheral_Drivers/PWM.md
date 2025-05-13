# PWM

### Regular PWM

For example: Configure GPIO0_C0 (pwm1m0) on the K1 expansion pin as PWM.

Note: Any IO can only be configured with one function. If you need to modify an already used IO, you need to find the corresponding position and comment out the original usage.

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi

+&pwm1 {
+    status = "okay";
+    pinctrl-0 = <&pwm1m0_pins>;
+};
```

Refer to the testing method in: 12-Hardware Function Testing [PWM](12-硬件功能测试.md#PWM)

### PWM for Infrared Use

To permanently disable the infrared remote control of the board, modify the rk3568-kickpi-ir.dtsi file in the device tree directory.

Taking the Android_SDK as an example:

```diff
---kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-kickpi-ir.dtsi
+++kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-kickpi-ir.dtsi

&pwm7 {
-   status = "okay";
+   status = "disabled";
    compatible = "rockchip,remotectl-pwm";
```