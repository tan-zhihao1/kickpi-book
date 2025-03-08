# 22 - Kernel driver customization

## configure defconfig

Android kernel configuration

```
$./build.sh -M
```

Linux kernel configuration

```
$./build.sh kernel-config
```

kernel path

```
Kernel-5.10
```

kernel config command common configuration

```
CD kernel-5.10/
make ARCH=arm64 menuconfig//According to the IC architecture, the current rk356x/3588 uses arm64.
//main command
make loadconfig			//restore old
make menuconfig			//Enter the configuration graphical interface
make saveconfig			//save as default configuration
make ARCH=arm64 rockchip_defconfig	//load defconfig in this path
```

> Configuration can be tracked by build.sh

Make saveconfig generate defconfig requires cp to be compiled into the * defconfig file used by the SDK for use

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> Different versions of defconfig may be different, you can find the corresponding version file in the git history.
>
> $ git log --name-only   kernel-5.10/ | grep defconfig



## dts

rk356x-linux

```
kernel/arch/arm64/boot/dts/rockchip/
	rk3562-kickpi-k3-linux.dts
	rk3568-kickpi-k1-linux.dts
	rk3568-kickpi-k1b-linux.dts
	rk3588-kickpi-k8-linux.dts
```

rk-android

```
kernel-5.10/arch/arm64/boot/dts/rockchip
 rk3568-kickpi-k1-android.dts
 rk3568-kickpi-k1b-android.dts
 rk3562-kickpi-k3-android.dts
 rk3588-kickpi-k8-android.dts
```



## PWM 配置

例如：将K1拓展引脚上的GPIO0_C0（pwm1m0）配置成PWM

注意：任何IO都只能配置一种功能，如果要修改已经有使用的IO需要找到对应位置注释掉原本的使用

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi

+&pwm1 {
+    status = "okay";
+    pinctrl-0 = <&pwm1m0_pins>;
+};
```

测试方法参考:12-硬件功能测试 [PWM](12-硬件功能测试.md#PWM)

## GPIO配置

GPIO常用配置一般是配成LED输出或者key输入，下面是对设备树这部分修改的示例，供参考修改

注意：任何IO都只能配置一种功能，如果要修改已经有使用的IO需要找到对应位置注释掉原本的使用

### LED配置

例如：将K1拓展引脚上的GPIO1_D4配置成LED，可以参考下面的修改

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
@@ -29,10 +29,10 @@ gpio1d0 {
         gpios = <&gpio1 RK_PD0 GPIO_ACTIVE_HIGH>;
         default-state = "off";
     };
+    gpio1d4 {
+        gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_HIGH>;
+        default-state = "off";
+    };
     gpio3b5 {
         gpios = <&gpio3 RK_PB5 GPIO_ACTIVE_HIGH>;
         default-state = "off";

```

测试方法参考:12-硬件功能测试 [LED](12-硬件功能测试.md#LED)

### gpio-key配置

例如：将K1拓展引脚上的GPIO1_D4配置成KEY_1，可以参考下面的修改

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
@@ -29,10 +29,10 @@ gpio1d0 {
         gpios = <&gpio1 RK_PD0 GPIO_ACTIVE_HIGH>;
         default-state = "off";
     };
-    gpio1d4 {
-        gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_HIGH>;
-        default-state = "off";
-    };
+    // gpio1d4 {
+    //     gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_HIGH>;
+    //     default-state = "off";
+    // };
     gpio3b5 {
         gpios = <&gpio3 RK_PB5 GPIO_ACTIVE_HIGH>;
         default-state = "off";

--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi
@@ -76,6 +76,21 @@ fan0: gpio-fan {
                status = "okay";
        };
        */
+
+       
+       gpio-keys {
+               compatible = "gpio-keys";
+               autorepeat;
+           status = "okay";
+       
+               key_1D4 {
+                       label = "GPIO_KEY_1D4";
+                       linux,code = <KEY_1>;
+                       gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_LOW>;
+                       debounce-interval = <100>;
+               };
+       };
+
 };
 
 &leds {
(END)
```

其中code值可以参考驱动中的宏定义

```
rk356x-linux\kernel\include\uapi\linux\input-event-codes.h
```

测试方法参考文档：12-硬件功能测试 [KEY 测试](12-硬件功能测试.md#KEYtest)



## LCD 配置

LCD屏幕配置，点亮不同的屏幕

配置参考文档：[RK3568 LCD配置](40-RK3568 LCD配置.md)



## External RTC module

Some boards do not have an onboard RTC module. Customers who need it can choose an external RTC module to solve the time preservation needs.

The following example is the approach of the K1B-Android external I2C-RTC module:

### Device hardware connection

#### IO ports used on the board:

![K1B-20PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250308164933546.png)

#### The physical wiring is as follows:

![image-20250308154357655](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250308154357655.png)

>CLK_OUT are generally not used
>
>The INT foot is connected according to demand. The K1B board does not have a pulled IO foot, so it is not connected.

#### device driver porting

​	The SDK of RK356x comes with driver porting for some models of RTC by default. If you find that you do not use the model yourself, you need to port it. The driver used in the example is hym8563 (the SDK comes with it), but the following is a brief introduction to the steps of driver porting if there is no driver.

RK-RTC drive path:

```
rk-android13.0\kernel-5.10\drivers\rtc\
```

>Put the kernel driver you got from the RTC manufacturer in this directory.
>
>Some manufacturers may not have the kernel driver code, only the single-chip microcomputer, so you can only figure it out yourself.

Modify the Makefile under the driver path:

```diff
+ obj-$(CONFIG_RTC_DRV_HYM8563)	+= rtc-hym8563.o 
```


Modify Kconfig under the driver path:

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

kernel enable

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

### Add rtc node on device tree I2C3

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

> rtc-irq-gpio is the IO port corresponding to the INT pin wiring. I didn't connect it in my test, so I commented it.

### Use date and hwclock to test whether the rtc driver is working properly

1. Display the current Linux system time

```shell
$ date
```

2. If the date time is not normal, such as 1970 or something, instead of my current time, such as 2009, then manually set the correct time yourself

```shell
$ date MMDDhhmm[[[CC]YY][.ss]]
```

>MM is the month (01-12)
>
> DD is the date (01-31)
>
> hh is the hour (00-23)
>
> mm is minutes (00-59) '
>
> [[CC] YY] is the year (optional, CC is the century, YY is the year)
>
> [.ss] is the number of seconds (optional)

3.Show hardware rtc time

```shell
$ hwclock
```
Since this is the first time the RTC driver has been loaded, the correct time has not been set, so the time displayed at this time is mostly incorrect in 1969 and 1970.
4. Set Linux system time to hardware rtc

```shell
$ hwclock -w
```

5. Check again whether the hardware rtc time is consistent with the system

```shell
$ hwclock
```

If the driver is working correctly, the time shown here should be the same as your current system time, which is said to be synchronized...
Otherwise, it means that your driver is not working correctly, and you have not correctly set the system time into the hardware RTC. You will have to go back and debug the driver yourself to find the reason.
6. Wait a few minutes for the power to be turned on after the power is cut off, and enter again:

```shell
$ hwclock
```

Normally, you will see that the time here has increased by a few minutes and seconds compared to the time shown in step 5.
That means the RTC is working normally.

### RTC interrupt test:

1. empty restore

```shell
$ echo 0 > /sys/class/rtc/rtc0/wakealarm
```

2. set time 

```shell
$ echo +60 > /sys/class/rtc/rtc0/wakealarm 
```

> +60：表示60秒后触发中断

View rtc information

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

Review system interrupt information:

```shell
$ cat /proc/interrupts
```

To see the interrupt visually, you can add time to the RTC interrupt print in the RTC intermediate function. You can refer to the following modification:

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

