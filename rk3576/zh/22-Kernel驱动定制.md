# 22-Kernel驱动定制

## version

```
Linux version 6.1
```



## cross compile

```
(android SDK)$ ls prebuilts/clang/host/linux-x86/clang-r487747c/bin/
(linux SDK)$ ls prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```



## dts

K7 dts 路径

```
(android SDK)$ ls kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android.dts
(linux SDK)$ ls kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-linux.dts
```



## defconfig

Kernel配置

```makefile
(android SDK)$ls kernel-6.1/arch/arm64/configs/rockchip_defconfig
(linux SDK)$ls kernel-6.1/arch/arm64/configs/rockchip_linux_defconfig
```

其他 config 路径

```
(android SDK)$ ls kernel-6.1/kernel/configs
android-14.config 
pcie_wifi.config 
	
(linux SDK)$ ls kernel-6.1/arch/arm64/configs/
rockchip_defconfig 
rk3576.config
```



## gpio-led 

**说明**

```
(SDK)/kernel-6.1/Documentation/devicetree/bindings/leds/leds-gpio.yaml
```

默认软件拓展 gpio 引脚配置为gpio-led进行控制，可以方便的输出高或低电平

查看GPIO列表，对应[K7拓展引脚](12-硬件功能测试.md#ExpansionPin-K7)章节

```shell
console:/ # ls /sys/class/leds/
GPIO2_D6  GPIO3_D5  GPIO4_B0  GPIO4_C7  PCIE_PWREN    mmc1::
GPIO2_D7  GPIO4_A4  GPIO4_B1  GPIO4_D0  SDMMC0_PWREN  work
GPIO3_D4  GPIO4_A6  GPIO4_C6  GPIO4_D1  fan
```

查看GPIO2_D6的状态，0为低电平，255为高电平

```
console:/ # cat /sys/class/leds/GPIO2_D6/brightness
0
```

设置GPIO2_D6为高电平

```
console:/ # echo 255 > /sys/class/leds/GPIO2_D6/brightness
```

设置GPIO2_D6为低电平

```
console:/ # echo 0 > /sys/class/leds/GPIO2_D6/brightness
```



## gpio-sysfs

**Pinctrl 引脚控制计算**  

Rockchip Pin的ID按照 控制器(bank)+端口(port)+索引序号(pin) 组成。

* 控制器和GPIO控制器数量一致

* 端口固定 A、B、C和D

* 索引序号固定 0、1、2、3、4、5、6、7

列举pin及引脚复用

```
cat /d/pinctrl/pinctrl-rockchip-pinctrl/pinmux-pins
```

计算方式

```
GPIO1-A1	= gpio1 - ((1 - 1) * 8 + 1)		= gpio1-1		= (1 * 32) + 1 		= 33
GPIO0-D3	= gpio0 - ((4 - 1) * 8 + 3)		= gpio0-27 		= (0 * 32) + 27 	= 27
```



## extend-40pin

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-extend-40pin.dtsi
```



## LCD MIPI

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7.dtsi
```

配置

```diff
$ vim kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7.dtsi
- // #include "rk3576-kickpi-lcd-mipi-8-800-1280-MX080B2140.dtsi"
+ #include "rk3576-kickpi-lcd-mipi-10.1-800-1280-AT101DS40I.dtsi"
//#include "rk3576-kickpi-lcd-mipi-10.1-800-1280-MX101BA1340.dtsi"
```

> 根据需要选择对应屏幕进行配置，重新编译即可



### PWM

PWM 框架在 /sys/class/pwm/ 目录下提供了用户层接口，详见 drivers/pwm/sysfs.c，PWM 驱动加载成功后，会在其下生成 pwmchipX 目录，如 pwmchip0、pwmchip1 等，此处的 X 与 PWM 的控制器或通道 id 无关，仅与 PWM 设备的 probe 顺序有关。

* dts 打开的节点

```
&pwm2_8ch_6 {
	pinctrl-0 = <&pwm2m2_ch6>;
	status = "okay";
};

&pwm2_8ch_7 {
	pinctrl-0 = <&pwm2m2_ch7>;
	status = "okay";
};

&pwm0_2ch_1 {
	pinctrl-0 = <&pwm0m0_ch1>;
	status = "okay";
};
```

* 确认 pwmchip 对应的 pwm



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

>从 dts 确认对应的节点 
>
>pwm0_2ch_1: pwm@27331000
>
>pwm2_8ch_6: pwm@2ade6000
>
>pwm2_8ch_7: pwm@2ade7000
>
>更多详见：kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576.dtsi



* 配置PWM通道

示例：设置 PWM0_CH1 通道，周期10000ns，占空比5000ns，极性为normal

根据上述信息可知，pwm0_ch1_m0 配置 dts 为 pwm0_2ch_1，即 pwmchip0

```
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```

> 按照示例配置参数设置成功后，可用万用表测量PWM3引脚，正确电压应为1.6V左右





## WIFI

* Android

```
RKDocs/android/wifi/
Rockchip_Introduction_REALTEK_WIFI_Driver_Porting_CN&EN.pdf
Rockchip_Introduction_WIFI_Configuration_CN&EN.pdf
```



## MMC

* Android

SD_Boot / SDMMC / SDIO / eMMC

```
RKDocs/common/MMC
Rockchip_Developer_Guide_SD_Boot_CN.pdf
Rockchip_Developer_Guide_SDMMC_SDIO_eMMC_CN.pdf
```





