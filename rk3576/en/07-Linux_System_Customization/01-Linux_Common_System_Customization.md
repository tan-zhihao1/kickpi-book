# 01-Linux_Common_System_Customization

## Symbol Explanation

* `SDK$`: Refers to the source code path.
* `console$`: Generally refers to the command-line console of the motherboard. [Motherboard command-line console](../02-Getting Started/02-Quick Start.md#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally refers to the environment where ADB can be run.

## System Platform

```
(SDK)$ source build/envsetup.sh
(SDK)$ lunch 
(SDK)$ get_build_var TARGET_BOARD_PLATFORM_PRODUCT
tablet
```

> Select rk3576_u-userdebug when running the `lunch` command.

## Boot Logo

The boot logo consists of two parts: the Uboot stage and the Kernel stage.

During the Uboot stage, the picture `kernel-6.1/logo.bmp` is parsed and displayed.
During the Kernel stage, the picture `kernel-6.1/logo_kernel.bmp` is parsed and displayed.

```
(SDK)$ ls kernel-6.1/*.bmp
kernel-6.1/logo.bmp  kernel-6.1/logo_kernel.bmp
```

## Boot Animation

```
(SDK)$ ls device/rockchip/common/bootanimation.zip
```

## Display Orientation Configuration

### Default Display Orientation Configuration

Modify the `SF_PRIMARY_DISPLAY_ORIENTATION` configuration. The rotation degrees can be configured as 0/90/180/270.

```
(SDK)$ vim device/rockchip/rk3576/BoardConfig.mk
# For Surface Flinger Rotation
SF_PRIMARY_DISPLAY_ORIENTATION ?= 0
SF_PRIMARY_DISPLAY_ORIENTATION ?= 90
SF_PRIMARY_DISPLAY_ORIENTATION ?= 180
SF_PRIMARY_DISPLAY_ORIENTATION ?= 270
```

### ADB Display Orientation Configuration

```
// 0°
(ADB)$ adb shell settings put system user_rotation 0
// 90°
(ADB)$ adb shell settings put system user_rotation 1
// 180°
(ADB)$ adb shell settings put system user_rotation 2
// 270°
(ADB)$ adb shell settings put system user_rotation 3
```

## Set Default Never Sleep

```diff
(SDK)$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <integer name="def_screen_off_timeout">60000</integer>
+    <integer name="def_screen_off_timeout">2147483647</integer>
```

## Set Default No Lock Screen

```diff
(SDK)$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <bool name="def_lockscreen_disabled">false</bool>
+    <bool name="def_lockscreen_disabled">true</bool>

$ vim device/rockchip/rk3576/overlay/frameworks/base/core/res/res/values/config.xml
+    <!-- Is the lock-screen disabled for new users by default -->
+    <bool name="config_disableLockscreenByDefault">true</bool>
```

## FAQ

### Problem of Exclamation Mark on the Signal

![image-20241127174458223](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127174458223.png)

The native Google system will send a request to the server `captive_portal_server` to determine whether the network is available.

If you don't want the exclamation mark to appear, configure the `captive_portal_server` and restart the device.

```
(ADB)$ adb shell settings put global captive_portal_server 204.gentool.top
```
