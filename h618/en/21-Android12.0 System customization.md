# 21-Android12.0 system customization

## TV desktop

Open the PRODUCT_PACKAGES corresponding to the tablet

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



## Tablet desktop

Open the PRODUCT_PACKAGES corresponding to the tablet

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



## APP and file compilation do not take effect

Android is a lazy compilation, which is directly replaced and then compiled. Some APPs and files may not be compiled into the mirror. 

They can be solved in one of the following ways

1. make installclean

2. rm out/target/product/apollo-p2/xxx/xxx

3. The compilation conditions change (such as file name, APK name).



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

persist.sys.locale attribute

```
device/softwinner/apollo/apollo_p2.mk
	PRODUCT_PROPERTY_OVERRIDES += \
		persist.sys.locale=zh-CN
```



persist.sys.country And the persist.sys.language property

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



| options | describe |
| ------- | -------- |
| en_US   | English  |
| zh_CN   | Chinese  |



locale Obtain priority. frameworks/base/core/jni/AndroidRuntime.cpp

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

Such as new test.apk 

h618-android12.0\vendor\aw\public\prebuild\apk Put your apk and Android.bp 

Android.mk way

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

Android.bp way

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

 /vendor/aw/homlet/homlet.mk add compilation

```makefile
PRODUCT_PACKAGES += \
       test
```



## Boot self-starting APP

### Launcher mode

Replace the desktop in this way, return HOME as, and replace the APP.

To make the APP boot as a Launcher, you need to add two categories to AndroidManifest.xml.

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```

H618 As a Launcher, there will be APP problems due to permission restrictions. You need to add permissions. Please refer to [APPspecial permission issue](## APP特殊权限问题)



There is no missing desktop attribute problem, you need to add the above two categories.

![image-20241101094329876](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241101094329876.png)



Replace the native desktop when preset, you need to add an overlay attribute

Android.bp way

```
    overrides: [
        "Launcher2",
        "Launcher3",
        "Launcher3QuickStep",
        "Launcher3QuickStepGo",
    ],
```

Android.mk way

```
LOCAL_OVERRIDES_PACKAGES := Launcher2 Launcher3 Launcher3QuickStep Launcher3QuickStepGo
```



### background call method

In this way, the APK can be called up by receiving the boot completion broadcast, and the original desktop can be returned.

**After receiving the boot broadcast and completing it, start the APP.**

1、Modify and increase the permissions of the APP and broadcast reception  AndroidManifest.xml

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



2、Add broadcast reception MyReceiver.java

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



3、Pre-install the app to /system/priv-app/

* Method 1, compile and pre-install into the source code, refer to, and build-in third-party APP.

* Method 2, push method pre-installed

  ```
  adb root; adb remount;
  adb push ./app /system/priv-app/
  ```
  

H618 is pre-installed into private-app. Due to permission restrictions, there will be APP problems. For adding permissions, please refer to the APP special permission problem.



4、Due to the relatively cumbersome permission issues, providing mirroring support wakes up the app under the /system/app/path

```
h618_android12_p2_uart0-bootup-test-20241022.img
```

* First confirm whether adb install can open apk normally 
* Uninstall apk 
* Install as system app through adb push

```
adb root; adb remount;
// adb push (要push的apk路径) 主板上路径
adb push ./app /system/priv-app/
adb reboot
```

* 开机后后台调用起APP



## APK priv-app push 替换

```
adb root; adb remount;  // 进行root 可以修改只读分区
adb push （你APK的路径） /system/priv-app/
adb reboot // 重启，android 会检索  /system/priv-app/ 的apk进行安装
```



## APP签名文件

签名文件路径如下

```
build/target/product/security/platform.pk8
build/target/product/security/platform.x509.pem
out/host/linux-x86/framework/signak.jar
```



## APP特殊权限问题

目前桌面级APP是特殊应用，如果添加特殊权限，可能会出现系统无法起来问题，需要抓取APP相关log日志

```shell
# logcat | grep com.example.myapplication3（Androidapk的包名）
09-14 10:36:06.662  3826  3826 W PackageManager: Privileged permission android.permission.INSTALL_PACKAGES for package com.example.myapplication3 (/system/priv-app/LauncherJingWei) not in privapp-permissions allowlist
09-14 10:36:08.437  3826  3826 W PackageManager: Privileged permission android.permission.INSTALL_PACKAGES for package com.example.myapplication3 (/system/priv-app/LauncherJingWei) not in privapp-permissions allowlist
```



根据log信息添加权限

```
SDK
	修改 frameworks/base/data/etc/privapp-permissions-platform.xml
	重新编译烧录

主板上
	/etc/permissions/privapp-permissions-platform.xml
```



根据 log 修改 android.permission.INSTALL_PACKAGES 内容如下，其他报错类似参考以下添加：

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



## 全编替换不生效问题

由于惰性编译，一些编译目标或依赖直接替换文件，在编译时不会拷贝生效

需要先手动清除，再进行编译

```
source build/envsetup.sh
lunch apollo_p2-userdebug
make installclean -j32
./build.sh
```





## HDMI 固定输出分辨率



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



## 网络ADB

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

#### 开机默认启动5555端口

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
```

