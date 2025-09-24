# Android常用系统定制

H618 Android 12.0 为官方 tv 系统，兼容 tv 版本的 APP，安装使用 tablet 或 phone 版本的 APP 可能会存在兼容性问题。



## TV 桌面

开启Tablet对应的PRODUCT_PACKAGES

```diff
--- a/vendor/aw/homlet/homlet.mk
+++ b/vendor/aw/homlet/homlet.mk
@@ -16,12 +16,12 @@ PRODUCT_PACKAGES += \
     DragonSN  \
     GalleryTV

-#PRODUCT_PACKAGES += \
+PRODUCT_PACKAGES += \
     TvLauncher \
     TvSettings

-PRODUCT_PACKAGES += \
       Launcher3 \
+#PRODUCT_PACKAGES += \
       Launcher3 \
        Settings \
        SystemUI
```

APK 路径

```
vendor/aw/homlet/package/TVLauncher
```



## Tablet 桌面

开启Tablet对应的PRODUCT_PACKAGES

```diff
--- a/vendor/aw/homlet/homlet.mk
+++ b/vendor/aw/homlet/homlet.mk
@@ -16,12 +16,12 @@ PRODUCT_PACKAGES += \
     DragonSN  \
     GalleryTV

-PRODUCT_PACKAGES += \
+#PRODUCT_PACKAGES += \
     TvLauncher \
     TvSettings

-#PRODUCT_PACKAGES += \
-       Launcher3 \
+PRODUCT_PACKAGES += \
+       Launcher3 \
        Settings \
        SystemUI
```

APK 路径

```
packages/apps/Launcher3/
```



## 屏幕方向与旋转

**ADB命令旋转屏幕**

支持使用adb命令进行旋转

```
// 正常显示
adb shell settings put system user_rotation 0

// 90°
adb shell settings put system user_rotation 1

// 180°
adb shell settings put system user_rotation 2

// 270°
adb shell settings put system user_rotation 3
```



**软件默认修改显示方向**

```
(SDK)$ vim android/device/softwinner/ceres-c3/ceres_c3.mk

PRODUCT_PROPERTY_OVERRIDES += \
    ro.surface_flinger.primary_display_orientation=ORIENTATION_0
    #ro.surface_flinger.primary_display_orientation=ORIENTATION_90
    #ro.surface_flinger.primary_display_orientation=ORIENTATION_270
```

> 支持 ORIENTATION_0、ORIENTATION_90、ORIENTATION_180、ORIENTATION_270

示例：修改屏幕默认显示旋转90°

```diff
(SDK)$ vim android/device/softwinner/ceres-c3/ceres_c3.mk

PRODUCT_PROPERTY_OVERRIDES += \
- ro.surface_flinger.primary_display_orientation=ORIENTATION_0
+ ro.surface_flinger.primary_display_orientation=ORIENTATION_90
```



## 开机LOGO

logo 替换路径

```
longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp
```



## 开机动画

支持 bootanimation.mp4 以及 bootanimation.zip 两种文件格式的开机动画

已知问题：mp4格式开机动画 系统第一次上电启动会显示安卓开机动画

两种文件同时存在播放 mp4 优先级比 zip 高 ，具体优先级见 frameworks/base/cmds/bootanimation/BootAnimation.cpp

第一步，存放动画文件（zip 或 mp4 格式）

```
device/softwinner/apollo/common/media/bootanimation/bootanimation.zip
device/softwinner/apollo/common/media/bootanimation/bootanimation.mp4
```

第二步，修改编译配置

bootanimation.zip 格式

```diff
SDK$ vim device/softwinner/apollo/common/media/config.mk
PRODUCT_COPY_FILES += \
-		$(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
+     	$(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
```

bootanimation.mp4 格式

```diff
SDK$ vim device/softwinner/apollo/common/media/config.mk
PRODUCT_COPY_FILES += \
-		$(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
+    	$(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
```



## 开机启动APP

如果需要开机启动APP，查看 [Android_Boot_Apk](../../../common/zh/android/Android_Boot_Apk.md) 文档。

```
kickpi-book/common/en/android/Android_Boot_Apk.md
kickpi-book/common/zh/android/Android_Boot_Apk.md
```



## 默认语言

persist.sys.locale 属性

```
$ vim device/softwinner/apollo/apollo_p2.mk
	PRODUCT_PROPERTY_OVERRIDES += \
		persist.sys.locale=zh-CN
```

persist.sys.country 以及 persist.sys.language 属性

```diff
--- a/device/softwinner/apollo/apollo_p2.mk
+++ b/device/softwinner/apollo/apollo_p2.mk
@@ -32,8 +32,8 @@ PRODUCT_PACKAGES += FT618

 PRODUCT_SYSTEM_DEFAULT_PROPERTIES += \
     persist.sys.timezone=Asia/Shanghai \
-    persist.sys.country=US \
-    persist.sys.language=en
+    persist.sys.country=CN \
+    persist.sys.language=zh
```

支持语言选项

| 选项  | 描述 |
| ----- | ---- |
| en_US | 英文 |
| zh_CN | 中文 |

locale 获取优先级见 

```
frameworks/base/core/jni/AndroidRuntime.cpp
```



## 预装APP

如果需要预装APK进xi'tong，查看 [Android_Apk_Signing](../../../common/zh/android/Android_Apk_Signing.md) 文档。

```
kickpi-book/common/en/android/Android_Apk_Signing.md
kickpi-book/common/zh/android/Android_Apk_Signing.md
```



## APK签名

如果需要给APK签名，查看 [Android_Apk_Signing](../../../common/zh/android/Android_Apk_Signing.md) 文档。

```
kickpi-book/common/en/android/Android_Apk_Signing.md
kickpi-book/common/zh/android/Android_Apk_Signing.md
```



## HDMI TX

hdmi tx 属性结点

```
# ls /sys/class/hdmi/hdmi/attr
avmute          dvi_mode   hdcp_enable  hpd_mask   phy_write  scdc_write
cec_dump        edid       hdcp_status  hpi_read   read       set_ddc
cec_enable      edid_test  hdcp_type    hpi_write  reg_dump   write
cec_simulation  esm_dump   hdmi_sink    phy_power  rxsense
debug           hdcp_dump  hdmi_source  phy_read   scdc_read
```



## 芯片序列号

芯片序列号可作为唯一识别ID使用，方法参考
[全志H芯片序列号的获取方法_全志 cpu id-CSDN博客](https://blog.csdn.net/yyzsyx/article/details/143108132)



## ADB 功能

### adb连接

![image-20250312140738317](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250312140738317.png)

板卡使用type-c线连接电脑，等板卡蓝灯闪烁时查看系统设备管理器是否识到是安卓设备，如果识别到的设备如图所示则需手动安装驱动

![image-20250226152046272](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152046272.png)

手动安装驱动

![370849870429aae4906079eb686ee326](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/370849870429aae4906079eb686ee326.jpg)

![image-20250226152541205](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152541205.png)

![image-20250226152555933](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152555933.png)

![image-20250226152618854](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152618854.png)

![image-20250226152635425](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152635425.png)

![image-20250226152643858](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152643858.png)

![image-20250226152654260](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152654260.png)

win键+R打开“运行” 输入cmd打开命令行

![image-20250226152824342](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152824342.png)

命令行中输入adb shell即可连接

系统若没有adb命令支持可自行参考安装[Windows 10 安装 ADB (Android Debug Bridge，Android 调试桥)-CSDN博客](https://blog.csdn.net/chengyq116/article/details/108291213)

![image-20250226152859458](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152859458.png)

**adb offline 问题**

```
$ adb devices
List of devices attached
6c00107150d147e1d52     offline
```

reconnect 即可

```
$ adb reconnect
reconnecting 6c00107150d147e1d52 [offline]
```

### 网络ADB

网口ADB需要先adb启动5555端口服务后

```
PS C:\Users\16708\Desktop> adb tcpip 5555
PS C:\Users\16708\Desktop> adb connect 192.168.77.170
connected to 192.168.77.170:5555
PS C:\Users\16708\Desktop> adb devices
List of devices attached
7c001075a313c7c1c12     device
192.168.77.170:5555     device

PS C:\Users\16708\Desktop> adb -s 192.168.77.170:5555 shell
apollo-p2:/ $ su
```

开机默认启动5555端口

需要通过USB adb操作

```
 adb root
 adb remount
 adb pull /system/build.prop
 //在build.prop中加入
 //添加这一行
 service.adb.tcp.port=5555
 //替换进入
 adb push .\build.prop /system/build.prop
 adb reboot
```



### ADB投屏

下载工具网盘路径

h618_data\5-DevelopmentTools开发工具\ADB Screen Cast 投屏

![image-20250303103950558](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250303103950558.png)

![image-20250303104027819](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250303104027819.png)



## 常见问题

### APP特殊权限问题

若APP为特殊应用（system-app 或 priv-app），如果没有相关权限，会出现系统无法起来问题，需要抓取 APP 相关 log 日志

```shell
# logcat | grep com.example.myapplication3
09-14 10:36:06.662  3826  3826 W PackageManager: Privileged permission android.permission.INSTALL_PACKAGES for package com.example.myapplication3 (/system/priv-app/LauncherJingWei) not in privapp-permissions allowlist
09-14 10:36:08.437  3826  3826 W PackageManager: Privileged permission android.permission.INSTALL_PACKAGES for package com.example.myapplication3 (/system/priv-app/LauncherJingWei) not in privapp-permissions allowlist
```

> com.example.myapplication3 为（Androidapk的包名）
>
> android.permission.INSTALL_PACKAGES 为预装APP缺少的权限

**方式一**

修改源码，根据 log 中的 android.permission.INSTALL_PACKAGES 报错添加对应 APP 的权限允许，其他报错类似：

```diff
--- a/frameworks/base/data/etc/privapp-permissions-platform.xml
+++ b/frameworks/base/data/etc/privapp-permissions-platform.xml
@@ -550,4 +550,8 @@ applications that come with the platform
     <privapp-permissions package="com.android.calllogbackup">
         <permission name="com.android.voicemail.permission.READ_VOICEMAIL"/>
     </privapp-permissions>
+
+    <privapp-permissions package="com.example.myapplication3">
+        <permission name="android.permission.INSTALL_PACKAGES"/>
+    </privapp-permissions>
```

**方式二**

修改主板上的 xml 文件

```
/etc/permissions/privapp-permissions-platform.xml
```



