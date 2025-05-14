# RTC

### Peripheral Wiring

The external PCF8563 RTC is connected to the I2C interface of the expansion port.  
**Note: Our expansion port does not include pull-up resistors. Please add them externally.**

![image-20241101111011769](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241101111011769.png)  
**Actual Wiring**  

![image-20241101111421019](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241101111421019.png)  

---

### Driver Porting

The H618 SDK includes the PCF8563 driver by default. Detailed porting steps are omitted here.  

**Driver Integration**  
1. Place the driver in `h618-android12.0\longan\kernel\linux-5.4\drivers\rtc`.  
2. Modify the `Makefile`:  
```makefile
obj-$(CONFIG_RTC_DRV_PCF8563) += rtc-pcf8563.o
```
3. Modify the `Kconfig` to add:  
```
config RTC_DRV_PCF8563
    tristate "Philips PCF8563/Epson RTC8564"
    help
      If you say yes here you get support for the
      Philips PCF8563 RTC chip. The Epson RTC8564
      should work as well.

      This driver can also be built as a module. If so, the module
      will be called rtc-pcf8563.
```

**Kernel Configuration**  
Enable the driver in the kernel configuration:  
```diff
--- a/longan/kernel/linux-5.4/arch/arm64/configs/sun50iw9p1smp_h618_android_defconfig
+++ b/longan/kernel/linux-5.4/arch/arm64/configs/sun50iw9p1smp_h618_android_defconfig
@@ -463,7 +463,8 @@ CONFIG_MMC_BLOCK_MINORS=16
 CONFIG_MMC_SUNXI=y
 # CONFIG_MMC_SUNXI_V4P10X is not set
 CONFIG_RTC_CLASS=y
-CONFIG_RTC_DRV_SUNXI=y
+#CONFIG_RTC_DRV_SUNXI is not set
+CONFIG_RTC_DRV_PCF8563=y
 CONFIG_SUNXI_REBOOT_FLAG=y
 CONFIG_SUNXI_FAKE_POWEROFF=y
 CONFIG_DMADEVICES=y
```

---

### Add RTC Node to I2C3 in Device Tree

```diff
--- a/longan/device/config/chips/h618/configs/p2/linux-5.4/board.dts
+++ b/longan/device/config/chips/h618/configs/p2/linux-5.4/board.dts
@@ -622,6 +622,14 @@
        pinctrl-names = "default", "sleep";
        twi_drv_used = <1>;
        status = "okay";
+
+    pcf8563:pcf8563@51 {
+        compatible = "nxp,pcf8563";
+        reg = <0x51>;  /* I2C Address */
+
+        status = "okay";  /* Enable the device */
+               #clock-cells = <0>;
+    };
 };
```

---

### Testing RTC Functionality

**Basic Testing with `date` and `hwclock`**  
1. Display current system time:  
   ```
   $ date
   ```
2. If the system time is incorrect (e.g., 1970), set it manually:  
   ```
   $ date MMDDhhmm[[[YY]YY][.ss]]
   ```
3. Display RTC hardware time:  
   ```
   $ hwclock
   ```
4. Sync system time to RTC:  
   ```
   $ hwclock -w
   ```
5. Verify RTC time:  
   ```
   $ hwclock
   ```
6. Wait a few seconds and check again to confirm RTC is counting.  

**RTC Interrupt Testing**  
1. Set an alarm:  
   ```
   $ echo 0 > /sys/class/rtc/rtc0/wakealarm    # Clear existing alarms
   $ echo +60 > /sys/class/rtc/rtc0/wakealarm  # Set alarm for 60 seconds later
   ```
2. Check RTC status:  
   ```
   $ cat /proc/driver/rtc
   ```
3. Verify interrupt logs (add debug prints in the driver):  
   ```c
   static irqreturn_t hym8563_irq(int irq, void *dev_id) {
       // ...
       printk("kickpi %s %d\n", __FUNCTION__, __LINE__);  // Debug message
       // ...
   }
   ```

**Expected Results**  
![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/wps1.jpg)  

**Interrupt Log Example**  
![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/wps2.jpg)