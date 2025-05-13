# RTC

### 外设接线

外设pcf8563rtc接在扩展口中的i2c中
**注意：我们扩展口是不带上拉的，请在外接设备中加入。**

![image-20241101111011769](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241101111011769.png)

实际接线

![image-20241101111421019](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241101111421019.png)

### 设备驱动移植

​	H618的SDK默认自带有pcf8563的驱动移植，这里不做详细介绍

​	驱动移植
​	将驱动放到 h618-android12.0\longan\kernel\linux-5.4\drivers\rtc 下，

​	修改Makefile 增加 obj-$(CONFIG_RTC_DRV_PCF8563)	+= rtc-pcf8563.o 
​	修改Kconfig 增加

```
//make menuconfig 的选项
config RTC_DRV_PCF8563
	tristate "Philips PCF8563/Epson RTC8564"
	help
	  If you say yes here you get support for the
	  Philips PCF8563 RTC chip. The Epson RTC8564
	  should work as well.

	  This driver can also be built as a module. If so, the module
	  will be called rtc-pcf8563.

```

内核使能

```
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

### 设备树I2C3上增加rtc节点

```
--- a/longan/device/config/chips/h618/configs/p2/linux-5.4/board.dts
+++ b/longan/device/config/chips/h618/configs/p2/linux-5.4/board.dts
@@ -622,6 +622,14 @@
        pinctrl-names = "default", "sleep";
        twi_drv_used = <1>;
        status = "okay";
+
+    pcf8563:pcf8563@51 {
+        compatible = "nxp,pcf8563";
+        reg = <0x51>;  /* I2C 地址 */
+
+        status = "okay";  /* 表示设备可用 */
+               #clock-cells = <0>;
+    };
 };

```

***\*【用date和hwclock测试rtc驱动工作是否正常】
\****一般来说，最简单的测试rtc是否工作正常的步骤就是，在加载完rtc驱动后（或者本身驱动是已经编译进内核的）：
1.显示当前Linux系统时间
`date`
2.如果date时间也不正常，比如是1970年之类的，而不是我当前的时间，比如2009年，那么就自己手动设置一下正确时间
`date MMDDhhmm[[[YY]YY][.ss]][.ss]`
3.显示硬件rtc时间
`hwclock`
由于此处是rtc驱动第一次加载，还没设置正确的时间，所以此时显示的时间，多数是不正确的是1969，1970年。
4.将Linux系统时间设置到硬件rtc中
`hwclock -w`
5.再次查看硬件rtc时间是否和系统中的一致
`hwclock`
如果驱动正确工作的话，此处显示的时间，就应该和你当前的系统时间一致了，也就是传说中的，时间同步了。。。
否则，就说明你驱动工作不正确，没有正确地把系统时间设置进入硬件rtc中，就要你自己回去调试驱动，找原因去。
6.等个几秒，再次输入：
`hwclock`
正常的话，会看到此处的时间，相对步骤5中显示的时间，增加了对应的那几秒
那就说明rtc正常工作了。

#### RTC 中断测试：

设置闹钟

`echo 0 > /sys/class/rtc/rtc0/wakealarm`   //清空

`echo +60 > /sys/class/rtc/rtc0/wakealarm `    //设置时间 

查看rtc信息

`cat /proc/driver/rtc`

 

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/wps1.jpg) 

闹钟时间到RTC中断有打印（需要自己添加）

```
static irqreturn_t hym8563_irq(int irq, void *dev_id)
{
  struct hym8563 *hym8563 = (struct hym8563 *)dev_id;
  struct i2c_client *client = hym8563->client;
  struct mutex *lock = &hym8563->rtc->ops_lock;
  int data, ret;
  printk("kickpi %s %d\n",__FUNCTION__,__LINE__); //加的打印
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

结果如下：

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/wps2.jpg) 

