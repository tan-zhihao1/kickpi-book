# Kernel驱动定制



## dts

k7 安卓 dts 路径

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android.dts
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



### gpio-sysfs





### wifi

```
RKDocs/android/wifi/
```









