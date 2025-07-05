# Android开机启动APP



## 属性配置方式

属性配置方式是KICKPI定制系统功能，非原生系统功能。（若无法正常使用，需要咨询技术支持）

**启动原理**

`am start` 是 Android 系统中用于启动 Activity 的命令行工具，属于 Activity Manager（`am`）的一部分。它允许你通过命令行直接启动应用的特定界面，常用于测试、自动化脚本或调试场景。

示例：

手动启动设置APP

```
$ adb shell am start com.android.settings/com.android.settings.Settings
```

> com.android.settings 为APP的包名
>
> com.android.settings.Settings 为APP界面的类名



**ADB方式获取APP的包名和界面类名**

查看已安装应用

  ```
  $ adb shell pm list packages
  ```

查看应用的 Activity 信息

```
$ adb shell dumpsys package com.android.settings | grep -i activity
```



**ADB 方式配置**

```
$ adb shell
$ setprop persist.sys.bootAppPack $packname
$ setprop persist.sys.bootAppClass $classname
```

> 将属性配置为需要开机启动的APP包名和类名。

示例：

```
$ adb shell
$ setprop persist.sys.bootAppPack com.android.settings
$ setprop persist.sys.bootAppClass com.android.settings.Settings
```

> 配置完成后，开机都会启动设置



**SDK 方式配置**



添加的APP名为 TestLauncher，添加 TestLauncher 的编译。

```diff
--- a/vendor/aw/homlet/homlet.mk
+++ b/vendor/aw/homlet/homlet.mk
@@ -16,12 +16,12 @@ PRODUCT_PACKAGES += \
     DragonSN  \
     GalleryTV

PRODUCT_PACKAGES += \
+		TestLauncher
```



## **Launcher方式**

Android 开机会默认启动 HOME 属性的APP，下面介绍修改APP为带 HOME 属性的APP。

**APP 修改**

在APP 的 AndroidManifest.xml 中添加两个 category ， 重新编译生成带 HOME 属性的APP。

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```



**ADB 修改方式**

> 当安装好新的 Laucher APP，系统会存在多个HOME属性APP，开机会让你选择其中一个作为桌面启动。
>
> 若需要只启动一个APP，需要将原本的桌面APP删除。

卸载 tv 桌面app

``` 
pm uninstall --user 0 com.android.tv.launcher
```

卸载 tablet 桌面app

```
```



**SDK 修改方式**

内置开机启动 APP

比如新增 test.apk 

```
$ ls h618-android12.0/vendor/aw/public/prebuild/apk/ 
 
```



## 预置APK

示例：

预置 TestLauncher APK ，创建 TestLauncher 目录并存放 Android.mk 和 APK 文件。

```
$ ls h618-android12.0/vendor/aw/public/prebuild/TestLauncher 
Android.mk
TestLauncher.apk
```

Android.mk

```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)

LOCAL_MODULE_TAGS := optional
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)

LOCAL_CERTIFICATE := PRESIGNED

LOCAL_ENFORCE_USES_LIBRARIES := false
LOCAL_DEX_PREOPT := false

LOCAL_MODULE := TestLauncher

LOCAL_SRC_FILES := TestLauncher.apk

include $(BUILD_PREBUILT)
```


