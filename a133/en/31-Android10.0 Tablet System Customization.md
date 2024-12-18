# 31-Android10.0 Tablet System Customization

[TOC]



## Android environment

```
cd android
source build/envsetup.sh
lunch ceres_c3-userdebug
```



## developer mode

```
Operation steps:
Settings - > About tablet - > Click Build number - > Prompt that it has switched to developer mode

Developer Options Path:
Settings -> System -> Developer options
```



## APP and file compilation do not take effect

Android is a lazy compilation, which is directly replaced and compiled. Some APPs and files may not be compiled into the mirror.

It can be solved in one of the following ways

1. make installclean

2. rm out/target/product/apollo-p2/xxx/xxx

3. Compile conditions change (e.g. file name, APK name)



## boot logo

logo alternate path

```
longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp
```



## boot animation

Support bootanimation.mp4 and bootanimation.zip two file formats

The two files exist at the same time to play mp4 with a higher priority than zip. For the specific priority, see frameworks/base/cmds/bootanimation/BootAnimation.cpp



Modify path

```
device/softwinner/apollo/common/media/config.mk
```



bootanimation.zip format

```makefile
PRODUCT_COPY_FILES += \
     $(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
```



bootanimation.mp4 format

```makefile
PRODUCT_COPY_FILES += \
    $(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
```

* The boot animation started for the first time after burning the mirror is native Android, and a restart is required to display the replacement boot animation.



## default language

Persist.sys.locale property

```
device/softwinner/apollo/apollo_p2.mk
	PRODUCT_PROPERTY_OVERRIDES += \
		persist.sys.locale=zh-CN
```



persist.sys.country and persist.sys.language properties

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



PRODUCT_LOCALES configuration

```makefile
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```



| option | describe |
| ------ | -------- |
| en_US  | English  |
| zh_CN  | Chinese  |



Locale get priority frameworks/base/core/jni/AndroidRuntime.cpp

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



## Built-in third-party APP

For example, add test.apk 

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

add compilation

```makefile
PRODUCT_PACKAGES += \
       test
```



## Boot self-starting APP

### **Launcher mode**

To make the APP boot as a Launcher, you need to add two categories to AndroidManifest.xml.

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```



### background call method

**After receiving the boot broadcast, start the APP.**

1. Modify and increase the permissions of the APP and broadcast to receive AndroidManifest.xml

```
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



2, increase the broadcast reception MyReceiver.java

```
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



3.Pre-install the app to /system/priv-app/

* Method 1, compile and pre-install to the source code, refer to [Built-in third-party APP] (#6. Built-in third-party APP)

* Method 2, push method pre-installed

```
adb root; adb remount;
adb push ./app /system/priv-app/
```





