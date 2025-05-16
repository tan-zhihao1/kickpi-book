# GPIO

GPIO常用配置一般是配成LED输出或者key输入，下面是对设备树这部分修改的示例，供参考修改

注意：任何IO都只能配置一种功能，如果要修改已经有使用的IO需要找到对应位置注释掉原本的使用

## LED配置

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

**测试**

* 查看GPIO注册列表

```
$ ls /sys/class/leds/

gpio1b0/ gpio1b2/ gpio1d4/ gpio3b6/ 
gpio1a4/ gpio1b1/ gpio1d0/ gpio3b5/ gpio4c4/
```

* 命令行控制GPIO

```
$ echo 1 > /sys/class/leds/gpio1d4/brightness
$ echo 0 > /sys/class/leds/gpio1d4/brightness
```



## gpio-key配置

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

**测试**

* Android测试使用命令getevent:

```
console:/ # getevent
add device 1: /dev/input/event3
  name:     "sunxi-ir-uinput"
add device 2: /dev/input/event2
  name:     "gt9xxnew_ts"
add device 3: /dev/input/event0
  name:     "sunxi-ir"
add device 4: /dev/input/event1
  name:     "gpio-keys"
/dev/input/event2: 0001 014a 00000001
/dev/input/event2: 0003 0035 00000247
/dev/input/event2: 0003 0036 000001c3
/dev/input/event2: 0003 0030 0000001e
/dev/input/event2: 0003 0032 0000001e
```

* Linux测试使用evtest：

```
root@ubuntu2004:~# evtest 
No device specified, trying to scan all of /dev/input/event*
Available devices:
/dev/input/event0:      fe6e0030.pwm
/dev/input/event1:      rk805 pwrkey
/dev/input/event2:      rockchip-rk809 Headset
/dev/input/event3:      hdmi_cec_key
/dev/input/event4:      adc-keys
/dev/input/event5:      gpio-keys
Select the device event number [0-5]: 5
Input driver version is 1.0.1
Input device ID: bus 0x19 vendor 0x1 product 0x1 version 0x100
Input device name: "gpio-keys"
Supported events:
  Event type 0 (EV_SYN)
  Event type 1 (EV_KEY)
    Event code 2 (KEY_1)
Key repeat handling:
  Repeat type 20 (EV_REP)
    Repeat code 0 (REP_DELAY)
      Value    250
    Repeat code 1 (REP_PERIOD)
      Value     33
Properties:
Testing ... (interrupt to exit)
Event: time 1699275783.300685, type 1 (EV_KEY), code 2 (KEY_1), value 1
Event: time 1699275783.300685, -------------- SYN_REPORT ------------
Event: time 1699275783.573759, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.573759, -------------- SYN_REPORT ------------
Event: time 1699275783.610429, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.610429, -------------- SYN_REPORT ------------
Event: time 1699275783.647094, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.647094, -------------- SYN_REPORT ------------
Event: time 1699275783.647094, type 1 (EV_KEY), code 2 (KEY_1), value 0
Event: time 1699275783.647094, -------------- SYN_REPORT ------------

```



## sys gpio控制

当GPIO没有任何使用时，可以通过 /sys/class/gpio 进行控制

**PIN 脚计算**

GPIO1-D0 (gpio1-24)为例：

```
每组GPIO有32位：0-32
A（0-7）B（8-15）C（16-23）D（24-31）
GPIO1-D0  计算 Pin num = 32 * 1 + 24 = 56
```

**步骤一 确保GPIO没有任何使用**

先将对应GPIO引脚注释，`/sys/class/gpio/export` 只能导入未注册的 gpio，将设备树对应IO取消使能

**步骤二 编译镜像，重新烧录**

**步骤三 确认gpio未被注册**

```
cat /sys/kernel/debug/pinctrl/pinctrl-rockchip-pinctrl/pinmux-pins
```

> 未注册如下：pin 56 (gpio1-24): (MUX UNCLAIMED) (GPIO UNCLAIMED)

**步骤四 对IO口进行控制**

通过 /sys/class/gpio/export 注册 56并进行控制

注册

```
root@kickpi:~# echo  56 > /sys/class/gpio/export
```

查看是否生成

```
root@kickpi:~# ls /sys/class/gpio/
export  gpio56  gpiochip0  gpiochip352  unexport
```

注册后的结点内容

```
root@kickpi:~# ls /sys/class/gpio/gpio56
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

通过结点下的内容控制 gpio , 常用如下

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

