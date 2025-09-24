# 01-Android常用系统定制



## 开机LOGO与动画定制

定制开机LOGO和动画，查看 [Android_Logo_Bootanimation](../../../common/zh/android/Android_Logo_Bootanimation.md) 文档。

```
kickpi-book/common/en/android/Android_Logo_Bootanimation.md
kickpi-book/common/zh/android/Android_Logo_Bootanimation.md
```



## 开机自启动APP

定制开机启动APP，查看 [Android_boot_App](../../../common/zh/android/Android_boot_App.md) 文档。

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



## 默认屏幕方向

如果需要旋转屏幕，查看 [Android_Screen_Rotation](../../../common/zh/android/Android_Screen_Rotation.md) 文档。

```
kickpi-book/common/en/android/Android_Screen_Rotation.md
kickpi-book/common/zh/android/Android_Screen_Rotation.md
```



## 默认时区和语言

**时区修改**

persist.sys.locale 属性

```
device/softwinner/ceres-c3/ceres_c3.mk
	PRODUCT_PROPERTY_OVERRIDES += \
		persist.sys.locale=zh-CN
```

**语言修改**

PRODUCT_LOCALES 配置

```makefile
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```

persist.sys.country 以及 persist.sys.language 属性

```diff
--- a/device/softwinner/ceres-c3/ceres_c3.mk
+++ b/device/softwinner/ceres-c3/ceres_c3.mk
@@ -32,8 +32,8 @@ PRODUCT_PACKAGES += FT618

 PRODUCT_SYSTEM_DEFAULT_PROPERTIES += \
     persist.sys.timezone=Asia/Shanghai \
-    persist.sys.country=US \
-    persist.sys.language=en
+    persist.sys.country=CN \
+    persist.sys.language=zh
```

语言属性配置获取优先级见 frameworks/base/core/jni/AndroidRuntime.cpp。



## 默认隐藏状态栏和导航栏

``` 
setprop persist.sys.ban_sb true
setprop persist.sys.ban_nb true
reboot
```



## 系统壁纸

``` 
android\device\softwinner\ceres-c3\overlay\frameworks\base\core\res\res\drawable-sw720dp-nodpi\default_wallpaper.jpg
```



## 桌面定制

如果需要旋转屏幕，查看 [Android_Launcher](../../../common/zh/android/Android_Launcher.md) 文档。

```
kickpi-book/common/en/android/Android_Launcher.md
kickpi-book/common/zh/android/Android_Launcher.md
```



## ADB功能与投屏

使用ADB功能或ADB投屏，查看 [ADB教程](../../../common/zh/adb/ADB教程.md) 文档。

```
kickpi-book/common/en/android/Android_Launcher.md
kickpi-book/common/zh/android/Android_Launcher.md
```



## DPI 配置

系统默认DPI配置

````diff
--- a/android/device/softwinner/ceres-c3/ceres_c3.mk
+++ b/android/device/softwinner/ceres-c3/ceres_c3.mk
@@ -256,7 +256,7 @@ PRODUCT_SYSTEM_DEFAULT_PROPERTIES += \
 endif
 
 PRODUCT_PROPERTY_OVERRIDES += \
-    ro.sf.lcd_density=160
+    ro.sf.lcd_density=120
 
 #language pack
 PRODUCT_PRODUCT_PROPERTIES  += \
````

命令行配置DPI

```
(console)# wm density 120
```



## 开发者模式

```
操作步骤：
Settings -> About tablet ->  点击 Build number -> 提示已经切换到开发者模式

开发者选项路径：
Settings -> System -> Developer options
```



## 默认kernel打印等级

```diff
--- a/longan/device/config/chips/a133/configs/default/env.cfg
+++ b/longan/device/config/chips/a133/configs/default/env.cfg
@@ -7,6 +7,7 @@ nand_root=/dev/nand0p4
 mmc_root=/dev/mmcblk0p4
 init=/init
-loglevel=8
+loglevel=1
```





## 常见问题

### 1.安卓系统耳机一直是连接状态

我们喇叭和耳机是同一通路 所以为了喇叭能够直接使用所以把驱动里耳机连接状态默认开启，所以安卓会一直识别有耳机， 要想恢复可以还原以下修改

![538c0528ac148df300bf0408eada0234](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/538c0528ac148df300bf0408eada0234.jpg)
