# 01-Android常用系统定制

SDK - 指代源码路径

console - 指代调试控制台

ADB - Android Debug Bridge命令行工具，下文指代可运行ADB的环境



## 开机LOGO定制

开机LOGO分两个部分，分别是Uboot阶段、Kernel阶段

Uboot 阶段解析图片 kernel-5.10/logo.bmp

Kernel 阶段解析图片 kernel-5.10/logo_kernel.bmp

```
(SDK)$ ls kernel-5.10/*.bmp
logo.bmp
logo_kernel.bmp
```



## 开机动画定制

你制作的开机动画放到指定目录

```
(SDK)$ ls device/rockchip/common/bootshutdown/bootanimation.zip
```

打开配置

```diff
(SDK)$ vim device\rockchip\common\BoardConfig.mk
-BOOT_SHUTDOWN_ANIMATION_RINGING ?= false
+BOOT_SHUTDOWN_ANIMATION_RINGING ?= true
```



## 关机动画定制

关机动画存放指定目录

```
(SDK)$ ls device/rockchip/common/bootshutdown/shutdownanimation.zip
```



## 默认屏幕方向

**默认修改显示方向**

```
(SDK)$ vim device/rockchip/$(dev)/BoardConfig.mk
(SDK)$ vim device/rockchip/rk356x/BoardConfig.mk // K1

	SF_PRIMARY_DISPLAY_ORIENTATION := 0 	//0°
	SF_PRIMARY_DISPLAY_ORIENTATION := 90 	//90°
	SF_PRIMARY_DISPLAY_ORIENTATION := 180	//180°
	SF_PRIMARY_DISPLAY_ORIENTATION := 270	//270°
```

> $(dev) 根据主板cpu进行选择
>
> K1/K1B - rk356x
>
> K3 - rk3562
>
> K8 - rk3588



**ADB修改显示方向**

```
// 0°
(ADB)$ adb shell settings put system user_rotation 0

// 90°
(ADB)$ adb shell settings put system user_rotation 1

// 180°
(ADB)$ adb shell settings put system user_rotation 2

// 270°
(ADB)$ adb shell settings put system user_rotation 3
```



## 修改默认触摸方向

可以参考LCD配置文档：[单触摸屏驱动配置](../05-外设驱动/LCD.md#TouchDriver)



## 默认隐藏状态栏

最新的固件可以命名或者app发送广播控制

命令行隐藏状态栏

```shell
(ADB)$ am broadcast -a android.intent.action.HIDE_STATUSBAR_BAR
```

命令行显示状态栏

```shell
(ADB)$ am broadcast -a android.intent.action.SHOW_STATUSBAR_BAR
```

APP广播测试apk位于网盘下：

```
rk356x_data\3-SoftwareData软件资料\Navigation-Status-test\
```

Android13 默认隐藏状态栏修改如下：

```diff
--- a/frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/CentralSurfacesImpl.java
+++ b/frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/CentralSurfacesImpl.java
@@ -952,6 +952,8 @@ public class CentralSurfacesImpl implements CoreStartable, CentralSurfaces {
         mRegisterStatusBarResult = result;
 
         createAndAddWindows(result);
+        hideStatusBar();
 
         if (mWallpaperSupported) {
             // Make sure we always have the most current wallpaper info.
```

Android13默认禁止状态栏下拉修改如下：

```diff
--- a/frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/CommandQueue.java
+++ b/frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/CommandQueue.java
@@ -536,10 +536,12 @@ public class CommandQueue extends IStatusBar.Stub implements
 
     // TODO(b/118592525): add multi-display support if needed.
     public boolean panelsEnabled() {
-        final int disabled1 = getDisabled1(mDisplayTracker.getDefaultDisplayId());
-        final int disabled2 = getDisabled2(mDisplayTracker.getDefaultDisplayId());
-        return (disabled1 & StatusBarManager.DISABLE_EXPAND) == 0
-                && (disabled2 & StatusBarManager.DISABLE2_NOTIFICATION_SHADE) == 0;
+        return false;
+        // final int disabled1 = getDisabled1(mDisplayTracker.getDefaultDisplayId());
+        // final int disabled2 = getDisabled2(mDisplayTracker.getDefaultDisplayId());
+        // return (disabled1 & StatusBarManager.DISABLE_EXPAND) == 0
+        //         && (disabled2 & StatusBarManager.DISABLE2_NOTIFICATION_SHADE) == 0;
+        
     }
```



## 默认隐藏导航栏

最新的固件可以命名或者app发送广播控制

命令行隐藏导航栏

```shell
$ am broadcast -a android.intent.action.HIDE_NAVIGATION_BAR
```

命令行显示导航栏

```shell
$ am broadcast -a android.intent.action.SHOW_NAVIGATION_BAR
```

APP广播测试apk位于网盘下：

```
rk356x_data\3-SoftwareData软件资料\Navigation-Status-test\
```

Android13默认隐藏导航栏修改如下： 

```diff
--- a/frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/CentralSurfacesImpl.java
+++ b/frameworks/base/packages/SystemUI/src/com/android/systemui/statusbar/phone/CentralSurfacesImpl.java
@@ -952,6 +952,8 @@ public class CentralSurfacesImpl implements CoreStartable, CentralSurfaces {
         mRegisterStatusBarResult = result;
 
         createAndAddWindows(result);
+        hideNavigation();
 
         if (mWallpaperSupported) {
             // Make sure we always have the most current wallpaper info.
```

Android13默认使用手势导航：

```diff
--- a/device/rockchip/rk356x/overlay/frameworks/base/core/res/res/values/config.xml
+++ b/device/rockchip/rk356x/overlay/frameworks/base/core/res/res/values/config.xml
@@ -61,7 +61,7 @@
          0: 3 button mode (back, home, overview buttons)
          1: 2 button mode (back, home buttons + swipe up for overview)
          2: gestures only for back, home and overview -->
-    <integer name="config_navBarInteractionMode">0</integer>
+    <integer name="config_navBarInteractionMode">2</integer>
     <bool name="config_swipe_up_gesture_setting_available">true</bool>
 </resources>
 
```



## 内置第三方APP

参考 Settings2.apk 

rk-android13.0\vendor\rockchip\common\apps\Settings2 放你的apk和Android.bp 

Android.mk 方式

```
###############################################################################
# Settings2
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := Settings2
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_TAGS := optional
LOCAL_BUILT_MODULE_STEM := package.apk
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
#LOCAL_PRIVILEGED_MODULE :=
LOCAL_CERTIFICATE := PRESIGNED
#LOCAL_OVERRIDES_PACKAGES := 
LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
#LOCAL_REQUIRED_MODULES :=
#LOCAL_PREBUILT_JNI_LIBS :=
include $(BUILD_PREBUILT)

```

Android.bp 方式

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

在 PRODUCT_PACKAGES 加入编译目标

```makefile
(SDK)$ vim vendor/rockchip/common/apps/apps.mk
PRODUCT_PACKAGES += \
       Settings2
```





## 开机自启动APP

**通过接收开机广播完成后，启动APP**

1、修改增加 APP 的权限以及广播接收  AndroidManifest.xml

```
<uses-permission android:name="android.permission.START_ACTIVITIES_FROM_BACKGROUND" />
<uses-permission android:name="android.permission.USE_FULL_SCREEN_INTENT" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

<application>
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



3、预装app 到 /system/priv-app/

* 方式一，编译预装到源码，参考 [内置第三方APP](# 6. 内置第三方APP)

* 方式二，push 方式预装

  ```
  adb root; adb remount;
  adb push ./app /system/priv-app/
  ```

  



## 默认语言

```
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```



| 选项  | 描述 |
| ----- | ---- |
| en_US | 英文 |
| zh_CN | 中文 |



## 默认时区





## 默认永不休眠

```
$ vim device/rockchip/rk356x/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<integer name="def_screen_off_timeout">2147483647</integer>
```



## 默认禁止锁屏

```
$ vim frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<bool name="def_lockscreen_disabled">true</bool>
```



## APK签名

[06-ANDROID常用系统定制/02-Android_APK签名](02-Android_APK签名.md)



## 网络ADB

网口ADB需要先adb启动5555端口服务后

```
(ADB)$ adb tcpip 5555
(ADB)$ adb connect 192.168.77.170
connected to 192.168.77.170:5555
(ADB)$ adb devices
List of devices attached
7c001075a313c7c1c12     device
192.168.77.170:5555     device

(ADB)$ adb -s 192.168.77.170:5555 shell
apollo-p2:/ $ su
```

**开机默认启动5555端口**

需要通过USB adb操作

```diff
(ADB)$ adb root
(ADB)$ adb remount
(ADB)$ adb pull /system/build.prop .
(ADB)$ vim build.prop
+ service.adb.tcp.port=5555
(ADB)$ adb push .\build.prop /system/build.prop
```



## HDMI RX APK 定制

K8 有一路 HDMI RX 输入，Android 可以通过打开 HdmiIn APK 显示 HDMI RX 画面。 

HdmiIn APK 路径

```
(SDK)$ packages/apps/rkCamera2/
```

hdmi in 画面全屏配置

```diff
--- a/packages/apps/rkCamera2/src/com/android/rockchip/camera2/activity/MainActivity.java
+++ b/packages/apps/rkCamera2/src/com/android/rockchip/camera2/activity/MainActivity.java
@@ -207,7 +207,7 @@ public class MainActivity extends Activity implements
         super.onCreate(savedInstanceState);
         setContentView(R.layout.activity_main);
 
+        fullScreen();
-        //fullScreen();
         rootView = (RelativeLayout) findViewById(R.id.root_view);
         rootView.setOnClickListener(this);
```



## 修改debug口波特率

修改路径：

```
rk-android13.0\kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-android.dtsi
```

修改rockchip,baudrate内容：

```
fiq-debugger {
		compatible = "rockchip,fiq-debugger";
		rockchip,serial-id = <2>;
		rockchip,wake-irq = <0>;
		/* If enable uart uses irq instead of fiq */
		rockchip,irq-mode-enable = <1>;
		rockchip,baudrate = <1500000>;  /* Only 115200 and 1500000 */
		interrupts = <GIC_SPI 252 IRQ_TYPE_LEVEL_LOW>;
		pinctrl-names = "default";
		pinctrl-0 = <&uart2m0_xfer>;
		status = "okay";
	};
```



## rockchip 官方文档

rockchip官方在代码中存放了文档 RKDocs

```
(SDK)$ tree -L 1 RKDocs/
RKDocs/
├── android
└── common
```

