# RTC

有些板子没有板载RTC模块，有需求的客户就可以选择外接一个RTC模块解决时间保存需求

下面的例子是K1B-Android外接I2C-RTC模块的做法：

## 设备硬件连接

### 板子上使用的IO口：

![image-20250308154448877](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250308154448877.png)

## 实物接线如下：

![image-20250308154357655](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250308154357655.png)

>其中CLK_OUT一般不使用不接 INT脚根据需求接，因板子没有带上拉的IO脚故不接

## 设备驱动移植

​	RK356x的SDK默认自带有一部分型号的RTC的驱动，如果发现没有自己使用型号就需要移植。示例中使用的驱动为hym8563（SDK自带有了），但是下面还是简单介绍一下驱动移植的步骤。

RK-RTC驱动位置路径：

```
rk-android13.0\kernel-5.10\drivers\rtc\
```

> 将你从RTC厂家拿到的内核驱动放到此目录下。
>
> 有些厂家可能没有内核驱动的代码，只有单片机的，哪你只能自行将单片机驱动改成Linux内核驱动。

修改驱动路径下Makefile：

```diff
+ obj-$(CONFIG_RTC_DRV_HYM8563)	+= rtc-hym8563.o 
```


修改驱动路径下Kconfig ：

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

内核使能

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

## 设备树I2C3上增加rtc节点

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

> rtc-irq-gpio就是对应INT脚接线的IO口，我测试没有接，所以注释了

## 用date和hwclock测试rtc驱动工作是否正常

​	下面测试步骤适用所有系统，其中步骤6是最关键的测试。

1.显示当前Linux系统时间

```shell
$ date
```

2.如果date时间也不正常，比如是1970年之类的，而不是我当前的时间，比如2009年，那么就自己手动设置一下正确时间

```shell
$ date MMDDhhmm[[[CC]YY][.ss]]
```

>MM 是月份（01-12）
>
>DD 是日期（01-31）
>
>hh 是小时（00-23）
>
>mm  是分钟（00-59）` 
>
>[[CC]YY] 是年份（可选，CC是世纪，YY是年份）
>
>[.ss] 是秒数（可选）

3.显示硬件rtc时间

```shell
$ hwclock
```

由于此处是rtc驱动第一次加载，有些平台可能还没设置正确的时间，此时显示的时间会是是不正确的，如：1969，1970年。

4.将Linux系统时间设置到硬件rtc中

```shell
$ hwclock -w
```

5.再次查看硬件rtc时间是否和系统中的一致

```shell
$ hwclock
```

如果驱动正确工作的话，此处显示的时间，就应该和你当前的系统时间一致了，也就是传说中的，时间同步了。。。
否则，就说明你驱动工作不正确，没有正确地把系统时间设置进入硬件rtc中，就要你自己回去调试驱动，找原因去。
6.断电等个几分钟上电，再次输入：

```shell
$ hwclock
```

正常的话，会看到此处的时间，相对步骤5中显示的时间，增加了对应的那几分几秒
那就说明rtc正常工作了。

## RTC 中断测试：

1. 清空还原

```shell
$ echo 0 > /sys/class/rtc/rtc0/wakealarm
```

2. 设置时间 

```shell
$ echo +60 > /sys/class/rtc/rtc0/wakealarm 
```

> +60：表示60秒后触发中断

查看rtc信息

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

查看系统中断信息：

```shell
$ cat /proc/interrupts
```

想直观看到中断，可以在RTC中间函数中加时间到RTC中断打印，可以参考下面这种修改：

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



