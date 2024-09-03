# 22-Android12.0 Tablet系统定制

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



## APP 、文件编译不生效问题

android 为惰性编译，直接替换一些APP、文本文件不一定编译到镜像中

通过主板软件上的文件以及APP相关信息比较进行确认



解决办法

1. make installclean

2. rm out/target/product/apollo-p2/xxx/xxx

3. 编译的条件发生变化（比如文件名、APK名）



## 开机LOGO

logo 替换路径

```
longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp
```



## 开机动画

支持 bootanimation.mp4 以及 bootanimation.zip 两种文件格式的开机动画

两种文件同时存在播放 mp4 优先级比 zip 高 ，具体优先级见 frameworks/base/cmds/bootanimation/BootAnimation.cpp



修改路径

```
device/softwinner/apollo/common/media/config.mk
```



bootanimation.zip 格式

```makefile
PRODUCT_COPY_FILES += \
     $(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
```



bootanimation.mp4 格式

```makefile
PRODUCT_COPY_FILES += \
    $(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
```

* 烧录镜像后第一次启动的开机动画是原生Android，需要重启显示替换的开机动画



## 内置第三方 APP

新增 test.apk 

Android.bp

```makefile
android_app_import {
    name: "test",
    apk: "test.apk",

    //presigned: true,	// 使用原来的签名
    certificate: "platform",	// 系统签名
    dex_preopt: {
        enabled: true,
    },

    privileged: true,	// 特权应用
    //product_specific: true,
    //proprietary: true,
    enforce_uses_libs: false,
}
```

加入编译

```makefile
PRODUCT_PACKAGES += \
       test
```



## 开机自启动 APP

要让 APP 作为 Launcher 开机自启动，需要在 AndroidManifest.xml 中添加两个 category

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
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

