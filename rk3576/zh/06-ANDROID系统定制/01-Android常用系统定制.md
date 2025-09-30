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



## 开机LOGO与动画定制

定制开机LOGO和动画，查看 [Android_Logo_Bootanimation](../../../common/zh/android/Android_Logo_Bootanimation.md) 文档。

```
kickpi-book/common/en/android/Android_Logo_Bootanimation.md
kickpi-book/common/zh/android/Android_Logo_Bootanimation.md
```



## 开机自启动APP

定制开机启动APP，查看 [Android_Boot_Apk](../../../common/zh/android/Android_Boot_Apk.md) 文档。

```
kickpi-book/common/en/android/Android_boot_App.md
kickpi-book/common/zh/android/Android_boot_App.md
```



## 预装第三方APP

预装APP，查看 [Android_Preinstall_Apk](../../../common/zh/android/Android_Preinstall_Apk.md) 文档。

```
kickpi-book/common/en/android/Android_Preinstall_Apk.md
kickpi-book/common/zh/android/Android_Preinstall_Apk.md
```



## APP签名

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

