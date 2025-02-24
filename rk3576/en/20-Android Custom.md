# 20-Android system customization



## Default never sleep

```diff
$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <integer name="def_screen_off_timeout">60000</integer>
+    <integer name="def_screen_off_timeout">2147483647</integer>
```



## Default no lock screen

```diff
$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <bool name="def_lockscreen_disabled">false</bool>
+    <bool name="def_lockscreen_disabled">true</bool>

$ vim device/rockchip/rk3576/overlay/frameworks/base/core/res/res/values/config.xml
+    <!-- Is the lock-screen disabled for new users by default -->
+    <bool name="config_disableLockscreenByDefault">true</bool>
```



## Software Platform

```
$ get_build_var TARGET_BOARD_PLATFORM_PRODUCT
tablet
```



## FAQ

** Exclamation mark problem appears in the signal **

![image-20241127174458223](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127174458223.png)

Native Google will send a request to the server captive_portal_server, according to determine whether the network is available, if you do not want to appear, configure captive_portal_server, restart

```
adb shell settings put global captive_portal_server 204.gentool.top
```

