# PWM



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



Extend 40Pin 接口 包含多路PWM，具体引脚见[扩展引脚章节](#ExpansionPin-K7)。

下面以 PWM2_CH7_M2 进行说明。

![image-20250421155643696](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250421155643696.png)



pwmX_Ych_Z，X 表示控制器 id ，Y 表示当前控制器支持的通道总数，Z 表示通道 id 。

PWM2_CH7_M2，代表

> pwm0_2ch_1: pwm@27331000
>
> pwm2_8ch_6: pwm@2ade6000
>
> pwm2_8ch_7: pwm@2ade7000

列举相关pwm节点

```
$ ls /sys/class/pwm/
pwmchip0  pwmchip1  pwmchip2  pwmchip3
```

可查看对应pwm dts节点

```
$ cat /sys/class/pwm/pwmchip0/device/uevent | grep FULLNAME
OF_FULLNAME=/pwm@27331000
```

>表示 pwnchip0 对应位 PWM0 通道1

配置PWM通道

示例：设置 PWM0_CH1 通道，周期10000ns，占空比5000ns，极性为normal

```
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```

> 按照示例配置参数设置成功后，可用万用表测量PWM0引脚，正确电压应为1.6V左右



导出 PWM 通道

```
echo 0 > /sys/class/pwm/pwmchip0/export
```

设置 PWM 周期（频率）

```
echo x > /sys/class/pwm/pwmchip0/pwm0/period
```

设置 PWM 占空比

```
echo x > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
```

设置 PWM 极性

```
echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
echo inverted > /sys/class/pwm/pwmchip0/pwm0/polarity
```

启用 PWM 输出

```
echo 1/0 > /sys/class/pwm/pwmchip0/pwm0/enable
```



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



