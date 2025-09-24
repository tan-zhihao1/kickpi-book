# 01-Android常用系统定制



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 系统平台

```
(SDK)$ ./build.sh lunch 
(SDK)$ source device/rockchip/.BoardConfig.mk
(SDK)$ source build/envsetup.sh 
(SDK)$ lunch $TARGET_PRODUCT-$BUILD_VARIANT
(SDK)$ get_build_var TARGET_BOARD_PLATFORM_PRODUCT
tablet
```



## 开机LOGO

开机LOGO分两个部分，分别是Uboot阶段、Kernel阶段

Uboot阶段解析显示图片kernel-6.1/logo.bmp

Kernel阶段解析显示图片kernel-6.1/logo_kernel.bmp

```
(SDK)$ ls kernel-6.1/*.bmp
kernel-6.1/logo.bmp  kernel-6.1/logo_kernel.bmp
```



## 开机动画

```
(SDK)$ ls device/rockchip/common/bootanimation.zip
```



## 开机启动APP

[开机启动APP](../../)

安卓已适配自启动APP功能

利用属性 persist.sys.bootAppPack / persist.sys.bootAppClass 配置，开机会启动对应APP

```
$ adb shell
$ setprop persist.sys.bootAppPack $packname
$ setprop persist.sys.bootAppClass $classname
```

示例：

```
$ adb shell
$ setprop persist.sys.bootAppPack com.DeviceTest
$ setprop persist.sys.bootAppClass com.DeviceTest.DeviceTest
```



## APK签名

如果需要给APK签名，查看 [Android_Apk_Signing](../../../common/zh/android/Android_Apk_Signing.md) 文档。

```
kickpi-book/common/en/android/Android_Apk_Signing.md
kickpi-book/common/zh/android/Android_Apk_Signing.md
```



## 显示方向配置

**默认配置显示方向**

修改 SF_PRIMARY_DISPLAY_ORIENTATION 配置，配置旋转度数为0/90/180/270

```
(SDK)$ vim device/rockchip/rk3576/BoardConfig.mk
# For Surface Flinger Rotation
SF_PRIMARY_DISPLAY_ORIENTATION ?= 0
SF_PRIMARY_DISPLAY_ORIENTATION ?= 90
SF_PRIMARY_DISPLAY_ORIENTATION ?= 180
SF_PRIMARY_DISPLAY_ORIENTATION ?= 270
```

**ADB配置显示方向**

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



## 默认永不休眠

```diff
(SDK)$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <integer name="def_screen_off_timeout">60000</integer>
+    <integer name="def_screen_off_timeout">2147483647</integer>
```



## 默认无锁屏

```diff
(SDK)$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <bool name="def_lockscreen_disabled">false</bool>
+    <bool name="def_lockscreen_disabled">true</bool>

$ vim device/rockchip/rk3576/overlay/frameworks/base/core/res/res/values/config.xml
+    <!-- Is the lock-screen disabled for new users by default -->
+    <bool name="config_disableLockscreenByDefault">true</bool>
```



## FAQ

**信号出现感叹号问题**

![image-20241127174458223](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127174458223.png)

原生谷歌会向服务器 captive_portal_server 发送请求，根据判断网络是否可用

如果不想出现，配置 captive_portal_server ，重启即可

```
(ADB)$ adb shell settings put global captive_portal_server 204.gentool.top
```

