# 20-Android系统定制

## 开机动画



## 默认永不休眠

```diff
$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <integer name="def_screen_off_timeout">60000</integer>
+    <integer name="def_screen_off_timeout">2147483647</integer>
```



## 默认无锁屏

```diff
$ vim device/rockchip/rk3576/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
-    <bool name="def_lockscreen_disabled">false</bool>
+    <bool name="def_lockscreen_disabled">true</bool>

$ vim device/rockchip/rk3576/overlay/frameworks/base/core/res/res/values/config.xml
+    <!-- Is the lock-screen disabled for new users by default -->
+    <bool name="config_disableLockscreenByDefault">true</bool>
```



## 软件平台

```
$ get_build_var TARGET_BOARD_PLATFORM_PRODUCT
tablet
```



## FAQ

**信号出现感叹号问题**

![image-20241127174458223](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127174458223.png)

原生谷歌会向服务器 captive_portal_server 发送请求，根据判断网络是否可用

如果不想出现，配置 captive_portal_server ，重启即可

```
adb shell settings put global captive_portal_server 204.gentool.top
```

