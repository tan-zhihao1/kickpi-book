# 22-Kernel 驱动定制

## 配置defconfig

Android 内核配置

```
$ ./build.sh -M
```

linux 内核配置

```
$ ./build.sh kernel-config
```

内核路径

```
kernel-5.10
```

kernel config 命令通用配置

```
cd kernel-5.10/
make ARCH=arm64 menuconfig //根据IC架构，目前rk356x/3588选用arm64
//主要命令
make loadconfig //恢复旧的
make menuconfig //进入配置图形化界面
make saveconfig //保存为默认配置
make ARCH=arm64 rockchip_defconfig //加载这个路径下的defconfig
```

> 具体配置可通过 build.sh 进行追踪

make  saveconfig生成defconfig 需要cp 到SDK编译使用的*defconfig文件进行使用

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> 不同版本的defconfig可能不同，可在git历史记录中查找对应的版本文件
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



### sys gpio控制

当存在将拓展引脚配置为输入的需求，默认软件的gpio-led不能满足需求。

将GPIO释放出来，通过 /sys/class/gpio 进行控制

**PIN 脚计算**

GPIO1-D0 (gpio1-24)为例：

```
每组GPIO有32位：0-32
A（0-7）B（8-15）C（16-23）D（24-31）
GPIO1-D0  计算 Pin num = 32 * 1 + 24 = 56
```



**步骤一**

先将对应GPIO引脚注释，`/sys/class/gpio/export` 只能导入未注册的 gpio

将设备树对应IO取消使能

设备树位于：

```
kernel-5.10/arch/arm64/boot/dts/rockchip/
40pin:	rk3568-kickpi-extend-40pin.dtsi
20pin:	rk3568-kickpi-extend-20pin.dtsi
```

**步骤二**

​		编译镜像，重新烧录

**步骤三**

​		确认gpio未被注册

```
cat /sys/kernel/debug/pinctrl/pinctrl-rockchip-pinctrl/pinmux-pins
```

> 未注册如下：pin 56 (gpio1-24): (MUX UNCLAIMED) (GPIO UNCLAIMED)

**步骤四**

​		通过 /sys/class/gpio/export 注册 56并进行控制

```
// 注册
root@kickpi:~# echo  56 > /sys/class/gpio/export
// 查看是否生成
root@kickpi:~# ls /sys/class/gpio/
export  gpio56  gpiochip0  gpiochip352  unexport
// 注册后的结点内容
root@kickpi:~# ls /sys/class/gpio/gpio56
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

​			通过结点下的内容控制 gpio , 常用如下

```
direction
	in / out
	echo in > /sys/class/gpio/gpio56/direction
	echo out > /sys/class/gpio/gpio56/direction
value
	0 / 1
	cat /sys/class/gpio/gpio56/value 		// 读取
	echo 1 > /sys/class/gpio/gpio56/value	// 配置高电平
	echo 0 > /sys/class/gpio/gpio56/value  // 配置低电平
```





## PWM 配置

例如：将K1拓展引脚上的GPIO配置成PWM





## GPIO_KEY配置

​	GPIO需要配置输入或者按键时可以配置成KEY模式

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

测试方法参考：[KEY test](KEY test)

