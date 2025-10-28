# ADC



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 文档参考

Android_SDK

```
RKDocs/common/SARADC/
```

Linux_SDK

```
docs/cn/Common/SARADC/
docs/en/Common/SARADC/
```

​	

## 拓展引脚 PWM

Extend Pin 接口包含多路PWM，具体引脚见对应主板扩展引脚。

K1 / K1B / K3 / K8 扩展引脚

[kickpi-book/rk356x-rk3588/zh/02-入门必读/04-扩展引脚](../../../rk356x-rk3588/zh/02-入门必读/04-扩展引脚)

K7 / K7C 扩展引脚

[kickpi-book/rk3576/zh/02-入门必读/04-扩展引脚](../../../rk3576/zh/02-入门必读/04-扩展引脚)



## ADC引脚示例

以 K7 为例， ADC位于扩展引脚中，见下图 36 、38 、40 号引脚。

![image-20250422110732652](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250422110732652.png)

> 由图可知，K7 扩展引脚中包含三路 SARADC 通道。
>
> SARADC_VIN* 对应主板接口上的对应序号通道。
>
> SARADC_VIN4 对应 /sys/bus/iio/devices/iio\:device0/in_voltage4_raw，其他同理。



## 驱动

```
drivers/iio/adc/rockchip_saradc.c
```



## DTS节点配置

```
Documentation/devicetree/bindings/iio/adc/rockchip-saradc.txt
```



## **SARADC常用接口**

**用户态接口**

可以通过用户态接口获取 adc 值，其中 * 表示 adc 第多少通道:

```
console$ cat /sys/bus/iio/devices/iio\:device0/in_voltage*_raw
```

示例：

读取通道4的电压值

```
console$ cat /sys/bus/iio/devices/iio\:device0/in_voltage4_raw
```



**内核常用接口**

获取adc值: iio_read_channel_raw()

获取电压: iio_read_channel_processed()
