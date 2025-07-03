# K5C主板供电

K5C 主板支持多种供电方式，注意硬件版本是否支持！

| 供电方式 | 供电电压 | 主板支持 |
| -------- | -------- | -------- |
| DC       | 12V      | >= V1.0  |
| Type-C   | 5V       | >= V1.1  |
| POE      | 12V      | >= V1.0  |
| BAT      | > 3.7V   | >= V1.2  |



## DC 供电

![image-20250508092200565](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508092200565.png)



## Type-C 供电

![image-20250508092317387](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508092317387.png)



## POE 供电

主板无法直接进行POE供电，需要外接POE模块，以POE供电插针式模块接入主板为例。



### 12V1A插针隔离POE模块

![image-20250508093405234](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508093405234.png)

![image-20250508095010489](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508095010489.png)



### POE模块接口

> 注意：VCC12V 不能接错，否则上电会损坏主板！

![image-20250508094612067](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508094612067.png)

### POE上电

1、接入POE模块

![image-20250508093811451](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508093811451.png)

2、接入带POE功能的网线，观察主板是否正常上电，指示灯正常工作。若指示灯异常，请立即拔出POE网线！



## BAT 供电

### 供电规格

使用电池供电时，最高峰值负载 7.5W。



### 电池接口

电池接口处，丝印对应PIN，VBAT连接电池正极，GND连接电池负极。

![image-20250508095732889](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508095732889.png)

### 电池上电

1、根据丝印连接电池正负极。

![image-20250508100049582](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/BAT.png)

2、接入电池后，长按Power按键进行开机，观察指示灯是否正常。若指示灯异常，请立即拔出电池接线！

> 接入电池时，仅支持按键进行开机！



### 电池充电功能

接入电池后，可通过Type-C接口或DC接口对电池进行充电。

若同时接入Type-C接口和DC接口，会优先使用DC进行充电。



### 软件配置

dts配置

```
longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
```

电池节点

``` dtd
battery_power_supply: battery-power-supply {
    compatible = "x-powers,axp803-battery-power-supply";
    status = "okay";

    pmu_chg_ic_temp = <0>;
    pmu_battery_rdc= <93>;
    pmu_battery_cap = <5067>;
    pmu_runtime_chgcur = <1000>;
    pmu_suspend_chgcur = <2000>;
    pmu_shutdown_chgcur = <2000>;
    pmu_init_chgvol = <4200>;
    pmu_battery_warning_level1 = <15>;
    pmu_battery_warning_level2 = <0>;
    pmu_chgled_func = <0>;
    pmu_chgled_type = <0>;
    ocv_coulumb_100 = <1>;
    pmu_bat_para1 = <0>;
    pmu_bat_para2 = <0>;
    pmu_bat_para3 = <0>;
	......
    pmu_bat_para31 = <100>;
    pmu_bat_para32 = <100>;

    pmu_bat_temp_enable = <0>;
    pmu_bat_charge_ltf = <1105>;
    pmu_bat_charge_htf = <121>;
    pmu_bat_shutdown_ltf = <1381>;
    pmu_bat_shutdown_htf = <89>;
    pmu_bat_temp_para1 = <2814>;
    pmu_bat_temp_para2 = <2202>;
	......
    pmu_bat_temp_para16 = <66>;

    wakeup_bat_out;
    /* wakeup_bat_in; */
    /* wakeup_bat_charging; */
    /* wakeup_bat_charge_over; */
    /* wakeup_low_warning1; */
    /* wakeup_low_warning2; */
    /* wakeup_bat_untemp_work; */
    /* wakeup_bat_ovtemp_work; */
    /* wakeup_bat_untemp_chg; */
    /* wakeup_bat_ovtemp_chg; */
};
```



### 调试信息

```
$ echo 8 > /proc/sys/kernel/printk
$ echo 0xf > /sys/class/axp/debug_mask
```





## 常见问题

### Type-C 供电异常问题

1、确认硬件是否支持Type-C供电。查看Type-C接口背面是否存在0欧电阻。

![image-20250218143136388](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250218143136388.png)

0欧电阻作用：Type-C 进行供电。

![image-20250218143226261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250218143226261.png)

2、检查Type-C供电是否足够，更换 **USB Type-C 充电器** 进行供电。



### 系统按键无法正常休眠问题

**情况一：是否接入了TypeC，进行了ADB连接。**

**情况二：查看是否配置了假待机**

```
consloe$ getprop persist.sys.kickpi_holding_wakelock 
```

**开启假待机**

假待机熄屏时仅关闭屏幕。

```
consloe$ setprop persist.sys.kickpi_holding_wakelock true
```

**关闭假待机**

```
consloe$ setprop persist.sys.kickpi_holding_wakelock false
```

**软件默认配置开启假待机**

```diff
--- a/android/device/softwinner/ceres-c3/ceres_c3.mk
+++ b/android/device/softwinner/ceres-c3/ceres_c3.mk
+PRODUCT_SYSTEM_DEFAULT_PROPERTIES += \
+    persist.sys.kickpi_holding_wakelock=true
```



### 电池供电重启问题

使用电池供电时，最高峰值负载 7.5W。如果超过峰值功率，PMIC 触发保护重启。



### 电池供电关机功耗问题

1、长按电源键进行关机

2、使用系统Power Off 功能进行关机

目前关机，电源指示灯仍然亮着，3.3V 和 5V 相关供电仍然存在；若需要关闭供电，需要调整硬件。

