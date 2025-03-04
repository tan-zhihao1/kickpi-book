# 20-Android系统定制

SDK - 指代源码路径

console - 指代调试控制台

ADB - Android Debug Bridge命令行工具，下文指代可运行ADB的环境

## 系统平台

```
(SDK)$ source build/envsetup.sh
(SDK)$ lunch 
(SDK)$ get_build_var TARGET_BOARD_PLATFORM_PRODUCT
tablet
```

> lunch 选择 rk3576_u-userdebug



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



## 显示旋转

Android系统显示旋转，可以修改如下配置，配置参数为0/90/180/270

```
# For Surface Flinger Rotation
SF_PRIMARY_DISPLAY_ORIENTATION ?= 0
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
adb shell settings put global captive_portal_server 204.gentool.top
```

