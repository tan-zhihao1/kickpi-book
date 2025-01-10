# 22-Kernel驱动定制

## version

```
Linux version 6.1.75
```



## cross compile

```
prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
```



## dts

k7 安卓 dts 路径

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android.dts
```

k7 Linux dts 路径

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-linux.dts
```



## defconfig

Android defconfig 配置

```makefile
arch/arm64/configs/rockchip_defconfig
```



Linux defconfig 配置

```
arch/arm64/configs/rockchip_defconfig
```



其他 config 路径

```
kernel-6.1/kernel/configs
	android-14.config 
	pcie_wifi.config 
	
kernel-6.1/arch/arm64/configs/
	rockchip_defconfig 
	rk3576.config
```



注意

```
make rockchip_defconfig android-14.config sdio_wifi.config rk3576.config
是依次加载覆盖的，如果编译后.config文件中没有开启对应的配置，需要检测 config 中是否取消了对应的config配置
```



## gpio-led 

说明见

```
(源码)/kernel-6.1/Documentation/devicetree/bindings/leds/leds-gpio.yaml
```



默认软件拓展 gpio 引脚配置为gpio-led进行控制，可以方便的输出高或低电平

```shell
// 查看可以控制的GPIO
console:/ # ls /sys/class/leds/
GPIO2_D6  GPIO3_D5  GPIO4_B0  GPIO4_C7  PCIE_PWREN    mmc1::
GPIO2_D7  GPIO4_A4  GPIO4_B1  GPIO4_D0  SDMMC0_PWREN  work
GPIO3_D4  GPIO4_A6  GPIO4_C6  GPIO4_D1  fan

// 查看GPIO2_D6的状态，0为低电平，255为高电平
console:/ # cat /sys/class/leds/GPIO2_D6/brightness
0

// 设置GPIO2_D6为高电平
console:/ # echo 255 > /sys/class/leds/GPIO2_D6/brightness

// 设置GPIO2_D6为低电平
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



## MIPI

mipi屏幕选择





## WIFI

```
RKDocs/android/wifi/
Rockchip_Introduction_REALTEK_WIFI_Driver_Porting_CN&EN.pdf
Rockchip_Introduction_WIFI_Configuration_CN&EN.pdf
```



## MMC

SD_Boot / SDMMC / SDIO / eMMC

```
RKDocs/common/MMC
Rockchip_Developer_Guide_SD_Boot_CN.pdf
Rockchip_Developer_Guide_SDMMC_SDIO_eMMC_CN.pdf
```





