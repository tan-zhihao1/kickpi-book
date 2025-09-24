# Android_boot_App

## 属性配置方式

属性配置方式是KICKPI Android 系统客制功能。（若无法正常使用，需要咨询技术支持）



**ADB 方式配置（无需修改源码）**

第一步，安装开机启动的APP；（接入U盘，ADB等方式安装）

第二步，获取对应APP的包名和页面类名；[点击查看获取方式和测试方法](#am_start_app)

第三步，配置属性；

```
$ adb shell
$ setprop persist.sys.bootAppPack $packname
$ setprop persist.sys.bootAppClass $classname
```

> `packname` 包名
>
> `classname` 类名
>
> 将属性配置为需要开机启动的APP包名和类名。

示例：

```
$ adb shell
$ setprop persist.sys.bootAppPack com.android.settings
$ setprop persist.sys.bootAppClass com.android.settings.Settings
```

第四步，重启；

```
$ adb reboot
```



**SDK 方式配置（修改源码定制镜像）**

第一步，[预装APK](#pre_installed_app)，如 TestLancher；

第二步，获取对应APP的包名和页面类名；[点击查看获取方式和测试方法](#am_start_app)

第三步，添加的APP名为 TestLauncher，添加 TestLauncher 的编译。

a133 路径

```
device/softwinner/apollo/apollo_p2.mk
```



```diff
--- a/vendor/aw/homlet/homlet.mk
+++ b/vendor/aw/homlet/homlet.mk
@@ -16,12 +16,12 @@ PRODUCT_PACKAGES += \
     DragonSN  \
     GalleryTV

+PRODUCT_PACKAGES += \
+		TestLauncher
```

第四步，添加包名和类名属性配置

```diff
--- a/device/softwinner/apollo/apollo_p2.mk
+++ b/device/softwinner/apollo/apollo_p2.mk
@@ -25,7 +25,8 @@ PRODUCT_COPY_FILES += $(PRODUCT_PREBUILT_PATH)/bImage:kernel

+ PRODUCT_PROPERTY_OVERRIDES += \
+	persist.sys.bootAppPack=com.android.TestLauncher \
+	persist.sys.bootAppClass=com.android.TestLauncher.Activity

```

第五步，编译SDK，烧录镜像；



## **Launcher方式**

Android 开机会默认启动 HOME 属性的APP，下面介绍修改APP为带 HOME 属性的APP。

**APP 修改**

第一步，在APP 的 AndroidManifest.xml 中添加两个 category；

第二步，重新编译生成带 HOME 属性的APP。

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```



**ADB 修改方式（无需修改源码）**

第一步，安装开机启动的APP；（接入U盘，ADB等方式安装）

第二步，卸载原生桌面；

卸载 tv 桌面app

``` 
$ adb shell
$ su
$ pm uninstall --user 0 com.android.tv.launcher
```

卸载 tablet 桌面app

```
$ adb shell
$ su
$ pm uninstall --user 0 com.android.launcher3
```

> 当安装好新的 Laucher APP，系统会存在多个HOME属性APP，系统会选择优先级高的优先启动。
>
> 需要只启动一个APP，需要将原本的桌面APP删除。

第三步，重启；

```
$ adb reboot
```



**SDK 修改方式（修改源码定制镜像）**

第一步，[预装APK](#pre_installed_app)，如 TestLancher；

第二步，修改所有的 TvLauncher 和 Launcher3 为预置APK的名称，如 TestLauncher；

```diff
--- a/vendor/aw/homlet/homlet.mk
+++ b/vendor/aw/homlet/homlet.mk
@@ -16,12 +16,12 @@ PRODUCT_PACKAGES += \
     DragonSN  \
     GalleryTV

PRODUCT_PACKAGES += \
-     	TvLauncher \
+		TestLauncher \
		TvSettings

PRODUCT_PACKAGES += \
-      	Launcher3 \
+		TestLauncher \
        Settings \
        SystemUI
```

第三步，编译SDK，烧录镜像；



## 预装APK<a id="pre_installed_app"> </a>

示例：预置 TestLauncher APK 

第一步，创建 TestLauncher 目录

第二步，存放 Android.mk 和 APK 文件。

```
$ ls h618-android12.0/vendor/aw/public/prebuild/TestLauncher 
Android.mk
TestLauncher_20250101.apk
```

Android.mk 文件编写示例：

```
# Copyright (C) 2008 The Android Open Source Project
# Copyright (C) 2012 Broadcom Corporation
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
#

LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := optional
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
LOCAL_CERTIFICATE := platform

LOCAL_MODULE := TestLauncher
LOCAL_PRIVILEGED_MODULE := true
LOCAL_ENFORCE_USES_LIBRARIES := true
LOCAL_DEX_PREOPT := true

LOCAL_OPTIONAL_USES_LIBRARIES := \
    androidx.window.extensions \
    androidx.window.sidecar

LOCAL_OVERRIDES_PACKAGES := \
    Launcher2 \
    Launcher3 \
    Launcher3QuickStep \
    Launcher3QuickStepGo

LOCAL_SRC_FILES := TestLauncher_20250101.apk

include $(BUILD_PREBUILT)
```

>`LOCAL_MODULE` 指定编译模块的名称
>
>`LOCAL_SRC_FILES` 指定编译文件的路径

第二步，修改apollo_p2.mk文件

路径device/softwinner/apollo/apollo_p2.mk

![image-20250814163119903](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250814163119903.png)

## 命令行启动APP<a id="am_start_app"> </a>

**am start 介绍**

`am start` 是 Android 系统中用于启动 Activity 的命令行工具，属于 Activity Manager（`am`）的一部分。它允许你通过命令行直接启动应用的特定界面，常用于测试、自动化脚本或调试场景。

示例：手动启动设置

```
$ adb shell am start com.android.settings/com.android.settings.Settings
```

> com.android.settings 为 APP 的包名
>
> com.android.settings.Settings 为 APP 界面的类名
>
> 只需要获取到 APP 的包名和界面类名，可以通过 am start 启动



**获取APP指定页面的包名和类名**

1. 手动打开对应界面
2. 执行命令获取当前活动的 Activity：

```
adb shell dumpsys window | grep mCurrentFocus
```

比如设置界面：

命令输出会包含类似 `com.android.settings/com.android.settings.Settings` 的信息，其中 `/` 前面是包名，后面是 Activity 类名，直接替换到 `am start -n` 命令中即可。



**获取APP的包名和界面类名**

查看已安装应用

  ```
$ adb shell pm list packages
  ```

查看应用的 Activity 信息

```
$ adb shell 
$ dumpsys package $packname | grep -i activity
```

示例：

```
$ adb shell 
$ dumpsys package com.android.settings | grep -i activity
```



## APP特殊权限问题

目前桌面级APP是特殊应用，如果添加特殊权限，可能会出现系统无法起来问题，需要抓取APP相关log日志

```shell
# logcat | grep LauncherTest
09-14 10:36:06.662  3826  3826 W PackageManager: Privileged permission android.permission.INSTALL_PACKAGES for package com.example.myapplication (/system/priv-app/LauncherTest) not in privapp-permissions allowlist
09-14 10:36:08.437  3826  3826 W PackageManager: Privileged permission android.permission.INSTALL_PACKAGES for package com.example.myapplication (/system/priv-app/LauncherTest) not in privapp-permissions allowlist
```

SDK 根据 log 修改 android.permission.INSTALL_PACKAGES 内容如下，其他报错类似参考以下添加：

```diff
--- a/frameworks/base/data/etc/privapp-permissions-platform.xml
+++ b/frameworks/base/data/etc/privapp-permissions-platform.xml
@@ -550,4 +550,8 @@ applications that come with the platform
     <privapp-permissions package="com.android.calllogbackup">
         <permission name="com.android.voicemail.permission.READ_VOICEMAIL"/>
     </privapp-permissions>
+
+    <privapp-permissions package="com.example.myapplication">
+        <permission name="android.permission.INSTALL_PACKAGES"/>
+    </privapp-permissions>
```

主板路径

```
/etc/permissions/privapp-permissions-platform.xml
```

