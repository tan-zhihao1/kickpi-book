# Kernel 驱动定制



### UART

K5C 拓展引脚中如PL2，PL3默认配置成GPIO，可复用成UART

单个引脚仅能用于一个功能，取消PL2，PL3 的GPIO配置

```diff
--- a/longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
+++ b/longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
@@ -1409,17 +1409,17 @@
                                linux,default_trigger = "default-on";
                        };
 
-                       PL2 {
-                               label = "PL2";
-                               gpios = <&r_pio PL 2 1 0 1 0>;
-                               linux,default_trigger = "default-on";
-                       };
-
-                       PL3 {
-                               label = "PL3";
-                               gpios = <&r_pio PL 3 1 0 1 0>;
-                               linux,default_trigger = "default-on";
-                       };
+                       // PL2 {
+                       //      label = "PL2";
+                       //      gpios = <&r_pio PL 2 1 0 1 0>;
+                       //      linux,default_trigger = "default-on";
+                       // };
+
+                       // PL3 {
+                       //      label = "PL3";
+                       //      gpios = <&r_pio PL 3 1 0 1 0>;
+                       //      linux,default_trigger = "default-on";
+                       // };
 
```

打开UART7

```
--- a/longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
+++ b/longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
+&uart7 {
+    status = "okay";
+};
```





