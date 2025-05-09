# ADC



> SDK$ - 下文指代源码路径
>
> console$ - 下文泛指主板命令行控制台
>
> ADB$ - Android Debug Bridge命令行工具，下文泛指可运行ADB的环境



## 硬件位置

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

