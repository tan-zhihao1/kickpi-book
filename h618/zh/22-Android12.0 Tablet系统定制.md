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



## 开机LOGO





## 开机动画

支持 bootanimation.mp4 以及 bootanimation.zip 两种方式的开机动画

两种文件同时存在播放优先级比 zip 高 ，优先级见 frameworks/base/cmds/bootanimation/BootAnimation.cpp

device/softwinner/apollo/common/media/config.mk

bootanimation.zip 方式

```makefile
PRODUCT_COPY_FILES += \
     $(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
```

bootanimation.mp4 方式

```makefile
PRODUCT_COPY_FILES += \
    $(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
```



## 内置第三方 APP

新增 test.apk

Android.bp

```makefile
android_app_import {
    name: "test",
    apk: "test.apk",

    presigned: true,
    //certificate: "platform",
    dex_preopt: {
        enabled: true,
    },

    //privileged: true,
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

