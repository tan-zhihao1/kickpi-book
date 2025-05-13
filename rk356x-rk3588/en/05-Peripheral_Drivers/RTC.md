# RTC

Some boards do not have an on-board RTC module. Customers with time-saving requirements can choose to connect an external RTC module.

The following example shows how to connect an I2C-RTC module externally to a K1B-Android board:

## Device Hardware Connection

### IO ports used on the board:

![image-20250222170554561](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222170554561.png)

## Physical wiring is as follows:

![image-20250308154357655](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250308154357655.png)

> Usually, the CLK_OUT pin is not used and not connected. The INT pin can be connected according to requirements. Since the board does not have IO ports with pull-up resistors, it is not connected here.

## Device Driver Transplantation

The RK356x SDK comes with drivers for some RTC models by default. If the model you are using is not included, you need to transplant the driver. In the example, the hym8563 driver is used (which is already included in the SDK), but the following briefly introduces the steps for driver transplantation.

RK-RTC driver location path:

```
rk-android13.0\kernel-5.10\drivers\rtc\
```

> Place the kernel driver obtained from the RTC manufacturer in this directory.
>
> Some manufacturers may not provide kernel driver code, only MCU code. In this case, you have to convert the MCU driver to a Linux kernel driver by yourself.

Modify the Makefile in the driver path:

```diff
+ obj-$(CONFIG_RTC_DRV_HYM8563)	+= rtc-hym8563.o 
```

Modify the Kconfig in the driver path:

```diff
+//make menuconfig
+config RTC_DRV_HYM8563
+	tristate "Haoyu Microelectronics HYM8563"
+	depends on OF
+	help
+	  Say Y to enable support for the HYM8563 I2C RTC chip. Apart
+	  from the usual rtc functions it provides a clock output of
+	  up to 32kHz.
+
+	  This driver can also be built as a module. If so, the module
+	  will be called rtc-hym8563.
```

Enable the kernel:

```diff
--- a/kernel-5.10/arch/arm64/configs/rockchip_defconfig
+++ b/kernel-5.10/arch/arm64/configs/rockchip_defconfig
@@ -864,7 +864,6 @@ CONFIG_LEDS_TRIGGER_BACKLIGHT=y
 CONFIG_LEDS_TRIGGER_DEFAULT_ON=y
 CONFIG_RTC_CLASS=y
+CONFIG_RTC_DRV_HYM8563=y
 CONFIG_DMADEVICES=y
 CONFIG_PL330_DMA=y
 CONFIG_RK_DMABUF_DEBUG=y

```

## Add an RTC node to I2C3 in the device tree

```diff
--- a/kernel-5.10/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-20pin.dtsi
+++ b/kernel-5.10/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-20pin.dtsi
@@ -15,6 +15,17 @@ 
&i2c3 {
     status = "okay";
     pinctrl-names = "default";
     pinctrl-0 = <&i2c3m0_xfer>;
+
+    hym8563: hym8563@51 {
+               compatible = "haoyu,hym8563";
+               reg = <0x51>;
+               #clock-cells = <0>;
+               clock-frequency = <32768>;
+               clock-output-names = "hym8563";
+               // IRQ GPIO IS MODIFYED FOR UPDATE HYM8562 DRIVER 
+               //rtc-irq-gpio = <&gpio0 RK_PC2 IRQ_TYPE_EDGE_FALLING>;
+       };
+
 };
```

> The rtc-irq-gpio corresponds to the IO port where the INT pin is connected. Since it was not connected in the test, it is commented out.

## Test whether the RTC driver works properly using `date` and `hwclock`

The following test steps are applicable to all systems. Step 6 is the most critical test.

1. Display the current Linux system time

```shell
$ date
```

2. If the `date` time is incorrect, for example, it shows a time like 1970 instead of the current time (e.g., 2009), manually set the correct time.

```shell
$ date MMDDhhmm[[[CC]YY][.ss]]
```

> MM is the month (01 - 12)
>
> DD is the day (01 - 31)
>
> hh is the hour (00 - 23)
>
> mm is the minute (00 - 59)
>
> [[CC]YY] is the year (optional, CC is the century, YY is the year)
>
> [.ss] is the second (optional)

3. Display the hardware RTC time

```shell
$ hwclock
```

Since this is the first time the RTC driver is loaded, some platforms may not have the correct time set yet. The displayed time may be incorrect, such as 1969 or 1970.

4. Set the Linux system time to the hardware RTC

```shell
$ hwclock -w
```

5. Check again whether the hardware RTC time is consistent with the system time

```shell
$ hwclock
```

If the driver is working correctly, the displayed time here should be consistent with the current system time, which means the time is synchronized. Otherwise, it indicates that the driver is not working correctly and has not set the system time to the hardware RTC properly. You need to go back and debug the driver to find the cause.

6. Power off the device, wait for a few minutes, then power it on again and enter:

```shell
$ hwclock
```

Normally, you will see that the time here has increased by the corresponding minutes and seconds compared to the time displayed in step 5, indicating that the RTC is working properly.

## RTC Interrupt Test:

1. Clear and restore

```shell
$ echo 0 > /sys/class/rtc/rtc0/wakealarm
```

2. Set the time

```shell
$ echo +60 > /sys/class/rtc/rtc0/wakealarm 
```

> +60: indicates that the interrupt will be triggered after 60 seconds

View RTC information

```shell
$ cat /proc/driver/rtc
rtc_time        : 07:16:39
rtc_date        : 2025-03-08
alrm_time       : 06:42:53
alrm_date       : 2025-03-08
alarm_IRQ       : yes
alrm_pending    : no
update IRQ enabled      : no
periodic IRQ enabled    : no
periodic IRQ frequency  : 1
max user IRQ frequency  : 64
24hr            : yes
```

View system interrupt information:

```shell
$ cat /proc/interrupts
```

To visually observe the interrupt, you can add a print statement for the RTC interrupt time in the RTC interrupt function. You can refer to the following modification:

```diff
---a\kernel-5.10\drivers\rtc\rtc-hym8563.c
+++b\kernel-5.10\drivers\rtc\rtc-hym8563.c

static irqreturn_t hym8563_irq(int irq, void *dev_id)
{
  struct hym8563 *hym8563 = (struct hym8563 *)dev_id;
  struct i2c_client *client = hym8563->client;
  struct mutex *lock = &hym8563->rtc->ops_lock;
  int data, ret;
+  printk("kickpi %s %d\n",__FUNCTION__,__LINE__); //add log
  mutex_lock(lock);

  /* Clear the alarm flag */
  data = i2c_smbus_read_byte_data(client, HYM8563_CTL2);
  if (data < 0) {
    dev_err(&client->dev, "%s: error reading i2c data %d\n",
      __func__, data);
    goto out;
  }
  data &= ~HYM8563_CTL2_AF;
  
  ret = i2c_smbus_write_byte_data(client, HYM8563_CTL2, data);
  if (ret < 0) {
   dev_err(&client->dev, "%s: error writing i2c data %d\n",
     __func__, ret);
  }
out:
  mutex_unlock(lock);
  return IRQ_HANDLED;
}
```