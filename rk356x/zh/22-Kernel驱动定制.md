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

kernel config 通用配置命令

```
cd kernel-5.10/
make loadconfig
make ARCH=arm64 menuconfig
make saveconfig
make ARCH=arm64 rockchip_defconfig
```
> ARCH=arm64&emsp;配置架构，目前rk356x/3588为arm64
> menuconfig&emsp;启动图形化配置界面
> saveconfig&emsp;将当前配置精简后保存为 `defconfig` 文件‌
> rockchip_defconfig&emsp;加载 Rockchip 官方预置的 ARM64 架构配置文件
> loadconfig&emsp;加载默认的defconfig

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> make  saveconfig生成defconfig 需要cp 到SDK编译使用的*defconfig文件进行使用
>
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



## PWM 配置

例如：将K1拓展引脚上的GPIO0_C0（pwm1m0）配置成PWM

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

​	GPIO常用配置一般是配成LED输出或者key输入，下面是对设备树这部分修改的示例，供参考修改

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

