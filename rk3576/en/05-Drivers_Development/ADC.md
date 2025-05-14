# ADC

## Symbol Explanation

* `SDK$`: Refers to the source code path.

* `console$`: Generally refers to the command-line console of the mainboard. [Mainboard Command-line Console](..\02-Getting_Started_Guide\02-Quick_Start_Guide.md#console_readme)

* `ADB$`: Android Debug Bridge command-line tool, generally refers to the environment where ADB can be run.

## Hardware Location

The ADC is located in the expansion pins, as shown in the following figure.

![image-20250422110732652](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250422110732652.png)

## **Read ADC Values**

```
console$ cat /sys/bus/iio/devices/iio\:device0/in_voltage*_raw
```

Example:

Read the voltage value of channel 4.

```
console$ cat /sys/bus/iio/devices/iio\:device0/in_voltage4_raw
```
