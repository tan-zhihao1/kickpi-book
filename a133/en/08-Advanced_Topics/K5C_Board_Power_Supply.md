# K5C_Board_Power_Supply

The K5C board supports multiple power supply methods. Verify hardware version compatibility!

| Power Supply Method | Voltage | Board Support |
| ------------------- | ------- | ------------- |
| DC                  | 12V     | >= V1.0       |
| Type-C              | 5V      | >= V1.1       |
| POE                 | 12V     | >= V1.0       |
| BAT                 | > 3.7V  | >= V1.2       |

---

## DC Power Supply

![image-20250508092200565](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508092200565.png)

---

## Type-C Power Supply

![image-20250508092317387](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508092317387.png)

---

## POE Power Supply

The board **cannot directly support POE** and requires an external POE module. Below is an example using a pin-header isolated POE module.

### 12V1A Pin-Header Isolated POE Module

![image-20250508093405234](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508093405234.png)

![image-20250508095010489](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508095010489.png)

### POE Module Interface

> **Warning**: Ensure **VCC12V** is connected correctly. Incorrect wiring may damage the board!

![image-20250508094612067](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508094612067.png)

### POE Power-On Steps

1. Connect the POE module:  
   ![image-20250508093811451](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508093811451.png)  
2. Connect a POE-enabled Ethernet cable. Check if the board powers on normally and LEDs function properly. **Disconnect immediately if LEDs behave abnormally!**

---

## BAT (Battery) Power Supply

### Battery Interface

Connect the battery’s positive terminal to **VBAT** and negative terminal to **GND** as labeled on the board:  
![image-20250508095732889](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508095732889.png)

### Battery Power-On Steps

1. Connect the battery according to the silk-screen labels:  
   ![image-20250508100049582](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/BAT.png)  
2. Press and hold the **Power button** to turn on the board. Check LED status. **Disconnect the battery immediately if LEDs behave abnormally!**  

> **Note**: The board can **only be powered on via the Power button** when using battery supply.

### Battery Charging

- The battery can be charged via **Type-C** or **DC** interfaces.  
- If both interfaces are connected, **DC charging takes priority**.

### Software Configuration

**Device Tree (DTS) Configuration**  
Path:  
```
longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
```
Battery Node:  
```dtd
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

### Debugging Commands

```bash
$ echo 8 > /proc/sys/kernel/printk
$ echo 0xf > /sys/class/axp/debug_mask
```

---

## FAQs

### Type-C Power Supply Issues

1. **Verify hardware compatibility**: Check if the Type-C interface has a **0Ω resistor** on the back (see below).  
   ![image-20250218143136388](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250218143136388.png)  
   **Purpose of 0Ω resistor**: Enables Type-C power supply.  
   ![image-20250218143226261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250218143226261.png)  

2. **Check power adapter**: Ensure the Type-C charger provides sufficient power. Try replacing the charger.

---

### System Fails to Enter Sleep Mode

**Case 1**: Check if the board is connected via **ADB over Type-C**.  
**Case 2**: Verify "fake sleep" configuration:  
```bash
console$ getprop persist.sys.kickpi_holding_wakelock 
```
**Enable fake sleep** (only turns off the screen):  
```bash
console$ setprop persist.sys.kickpi_holding_wakelock true
```
**Disable fake sleep**:  
```bash
console$ setprop persist.sys.kickpi_holding_wakelock false
```
**Default software configuration**:  
```diff
--- a/android/device/softwinner/ceres-c3/ceres_c3.mk
+++ b/android/device/softwinner/ceres-c3/ceres_c3.mk
+PRODUCT_SYSTEM_DEFAULT_PROPERTIES += \
+    persist.sys.kickpi_holding_wakelock=true
```