# 01-Android常用系统定制



## 开机LOGO定制

logo 源码替换路径

```
longan/device/config/chips/a133/configs/c3/android/bootlogo.bmp
```

> 图片格式 - 32bit
>
> 图片分辨率 - 小于实际屏幕分辨率



## 开机动画定制

### 默认替换开机动画

Android 支持 bootanimation.mp4 以及 bootanimation.zip 两种文件格式的开机动画。

两种文件同时存在播放 mp4 优先级比 zip 高 ，具体优先级见

```
frameworks/base/cmds/bootanimation/BootAnimation.cpp
```

第一步，替换源码动画；

动画源码路径如下

```
android/device/softwinner/ceres-c3/media/bootanimation.zip
```

第二步，修改动画拷贝方式；

bootanimation.zip 格式

```diff
$ vim android/device/softwinner/ceres-c3/ceres_c3.mk
PRODUCT_COPY_FILES += \
-		$(LOCAL_PATH)/media/bootanimation.mp4:system/media/bootanimation.mp4
+     	$(LOCAL_PATH)/media/bootanimation.zip:system/media/bootanimation.zip
```

bootanimation.mp4 格式

```diff
$ vim android/device/softwinner/ceres-c3/ceres_c3.mk
PRODUCT_COPY_FILES += \
-     	$(LOCAL_PATH)/media/bootanimation.zip:system/media/bootanimation.zip
+		$(LOCAL_PATH)/media/bootanimation.mp4:system/media/bootanimation.mp4
```



### **在线替换开机动画**

Android 支持在线替换开机动画

```
$ adb root
$ adb remount
$ adb push $(bootanimation_path) /system/media/bootanimation.zip
$ adb reboot
```



### bootanimation.zip 制作

**文件结构**

```
$ zipinfo android/device/softwinner/ceres-c3/media/bootanimation.zip
 part0/
 part0/0000.png
 part0/0001.png
 part0/0002.png
 ...
 part1
 part1/0013.png
 part1/0014.png
 part1/0015.png
 ...
 audio_conf.txt
 desc.txt
```

> bootanimation.zip 包含 part0 part1 文件夹和 desc.txt 文件，part0 part1 等文件夹里面放的是动画拆分的图片，格式为 png。



**desc.txt 配置文件**

```
800 480 15
p 1 0 part0 
p 0 0 part1

```

> 第一行
>
> WIDTH HEIGHT FPS
>
> WIDTH  800 图片的宽度
>
> HEIGHT 480 图片的高度
>
> FPS 15 帧数
>
> 第二、三行
>
> TYPE COUNT PAUSE PATH
>
> TYPE p 播放直到开机完成
>
> COUNT 1 播放次数，0代表无线循环直到开机结束
>
> PAUSE 0 part结束后暂停帧数
>
> PATH part1 路径
>
> 最后一行需为空行



**window 压缩打包zip**

1. 左键选中 part0、part1、desc.txt等文件；（保证part0、part1等文件位于压缩包根目录级别）
2. 右键选择压缩，选择其他；

3. 进行ZIP压缩，压缩标准需要选择 Store；

![image-20250408175807082](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250408175807082.png)



**Linux 压缩打包zip**

linux 压缩，指定压缩等级为最低等级stored，只归档不压缩

```
$ zip -0qry bootanimation.zip  *.txt part0/ part1/
```

> 注意压缩包下 part0 part1 txt 为同一级根目录



## 开机音乐定制

如需开机音乐，将开机音乐放入part0目录中，命名为Audio.wav。

在根目录加入audio_conf.txt，复制原有动画配置即可。

```
part0/audio.wav
audio_conf.txt
```



## 默认屏幕方向

修改默认显示方向

![image-20250606165333617](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250606165333617.png)

触控修改参考如下，注意选择对应屏幕的设备树文件

![image-20250606165428265](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250606165428265.png)



## 旋转功能

支持使用adb命令进行旋转

```
// 0
adb shell settings put system user_rotation 0

// 90°
adb shell settings put system user_rotation 1

// 180°
adb shell settings put system user_rotation 2

// 270°
adb shell settings put system user_rotation 3
```



## 隐藏状态栏和导航栏

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

源码路径

```
SDK$ ls android/packages/apps/Launcher3/
```



单编APK

```
SDK$ cd android/
SDK$ . build/envsetup.sh
SDK$ lunch ceres_c3-userdebug
SDK$ BUILD_NUMBER=ido-a133 m Launcher3QuickStepGo -j32
```



**关闭搜索框**

```diff
--- a/android/packages/apps/Launcher3/src/com/android/launcher3/config/BaseFlags.java
+++ b/android/packages/apps/Launcher3/src/com/android/launcher3/config/BaseFlags.java
@@ -68,7 +68,7 @@ abstract class BaseFlags {
     public static final boolean LAUNCHER3_PROMISE_APPS_IN_ALL_APPS = false;
 
     // Enable moving the QSB on the 0th screen of the workspace
-    public static final boolean QSB_ON_FIRST_SCREEN = true;
+    public static final boolean QSB_ON_FIRST_SCREEN = false;
 
     public static final TogglableFlag EXAMPLE_FLAG = new TogglableFlag("EXAMPLE_FLAG", true,
             "An example flag that doesn't do anything. Useful for testing");
```



## 默认语言

persist.sys.locale 属性

```
device/softwinner/apollo/apollo_p2.mk
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



PRODUCT_LOCALES 配置

```makefile
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```



| 选项  | 描述 |
| ----- | ---- |
| en_US | 英文 |
| zh_CN | 中文 |



locale 获取优先级 frameworks/base/core/jni/AndroidRuntime.cpp

```java
/*
 * Read the persistent locale. Inspects the following system properties
 * (in order) and returns the first non-empty property in the list :
 *
 * (1) persist.sys.locale
 * (2) persist.sys.language/country/localevar (country and localevar are
 * inspected iff. language is non-empty.
 * (3) ro.product.locale
 * (4) ro.product.locale.language/region
 *
 * Note that we need to inspect persist.sys.language/country/localevar to
 * preserve language settings for devices that are upgrading from Lollipop
 * to M. The same goes for ro.product.locale.language/region as well.
 */
const std::string readLocale()
{
    const std::string locale = GetProperty("persist.sys.locale", "");
    if (!locale.empty()) {
        return locale;
    }

    const std::string language = GetProperty("persist.sys.language", "");
    if (!language.empty()) {
        const std::string country = GetProperty("persist.sys.country", "");
        const std::string variant = GetProperty("persist.sys.localevar", "");

        std::string out = language;
        if (!country.empty()) {
            out = out + "-" + country;
        }

        if (!variant.empty()) {
            out = out + "-" + variant;
        }

        return out;
    }

    const std::string productLocale = GetProperty("ro.product.locale", "");
    if (!productLocale.empty()) {
        return productLocale;
    }

    // If persist.sys.locale and ro.product.locale are missing,
    // construct a locale value from the individual locale components.
    const std::string productLanguage = GetProperty("ro.product.locale.language", "en");
    const std::string productRegion = GetProperty("ro.product.locale.region", "US");

    return productLanguage + "-" + productRegion;
}
```



## 内置第三方 APP

比如新增 test.apk 

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

LOCAL_MODULE := test

LOCAL_SRC_FILES := $(LOCAL_MODULE).apk

include $(BUILD_PREBUILT)
```

Android.bp

```makefile
android_app_import {
    name: "test",
    apk: "test.apk",

    //presigned: true,
    certificate: "platform",
    dex_preopt: {
        enabled: true,
    },

    privileged: true,
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

### **Launcher方式启动**

要让 APP 作为 Launcher 开机自启动，需要在 AndroidManifest.xml 中添加两个 category

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```



### 后台调用方式

**通过接收开机广播完成后，启动APP**

1、修改增加 APP 的权限以及广播接收  AndroidManifest.xml

```xml
<uses-permission android:name="android.permission.START_ACTIVITIES_FROM_BACKGROUND" />
<uses-permission android:name="android.permission.USE_FULL_SCREEN_INTENT" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

<application
        <receiver
            android:name=".MyReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter android:priority="1000">
                <action android:name="android.intent.action.BOOT_COMPLETED"></action>
            </intent-filter>
        </receiver>
</application>
```



2、增加广播接收 MyReceiver.java

```java
package com.example.myapplication;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

public class MyReceiver extends BroadcastReceiver {
    public MyReceiver() {
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent.getAction().equals("android.intent.action.BOOT_COMPLETED")) {
            Intent i = new Intent(context, MainActivity.class);
            i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.startActivity(i);
            Log.i("kickpi", "onReceive: start app !!!");
        }
    }
}
```



3、预装app 到 /system/priv-app/

* 方式一，编译预装到源码，参考 [内置第三方APP](# 6. 内置第三方APP)

* 方式二，push 方式预装

  ```
  adb root; adb remount;
  adb push ./app /system/priv-app/
  ```




## ADB投屏

下载工具网盘路径

5-DevelopmentTools开发工具\ADB Screen Cast 投屏

![image-20250303103950558](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250303103950558.png)

![image-20250303104027819](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250303104027819.png)



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





## 常见问题

### 1.安卓系统耳机一直是连接状态

我们喇叭和耳机是同一通路 所以为了喇叭能够直接使用所以把驱动里耳机连接状态默认开启，所以安卓会一直识别有耳机， 要想恢复可以还原以下修改

![538c0528ac148df300bf0408eada0234](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/538c0528ac148df300bf0408eada0234.jpg)
