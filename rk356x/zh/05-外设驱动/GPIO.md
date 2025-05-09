# GPIO

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