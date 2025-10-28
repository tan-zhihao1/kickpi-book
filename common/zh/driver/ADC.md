# ADC



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 文档参考

Android_SDK

```
RKDocs/android/SARADC/
```

Linux_SDK

```
docs/cn/Linux/c/SARADC/
docs/en/Linux/Wifibt
```



## 拓展引脚 PWM

Extend Pin 接口包含多路PWM，具体引脚见对应主板扩展引脚。

[kickpi-book/rk356x-rk3588/zh/02-入门必读/04-扩展引脚](../../../rk356x-rk3588/zh/02-入门必读/04-扩展引脚)

[kickpi-book/rk3576/zh/02-入门必读/04-扩展引脚](../../../rk3576/zh/02-入门必读/04-扩展引脚)



### RK3576

ADC位于扩展引脚中，见下图

![image-20250422110732652](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250422110732652.png)



## **读取ADC值**

```
console$ cat /sys/bus/iio/devices/iio\:device0/in_voltage*_raw
```



示例：

读取通道4的电压值

```
console$ cat /sys/bus/iio/devices/iio\:device0/in_voltage4_raw
```

