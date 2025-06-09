# 01-Common_Android_System_Customizations

SDK - Refers to the source code path

console - Refers to the debugging console

ADB - Android Debug Bridge command-line tool. Hereinafter, it refers to an environment where ADB can be run.

## 1. Boot Logo Customization

The boot logo is divided into two parts: the Uboot stage and the Kernel stage.

The Uboot stage parses the image `kernel-5.10/logo.bmp`.

The Kernel stage parses the image `kernel-5.10/logo_kernel.bmp`.

```
(SDK)$ ls kernel-5.10/*.bmp
logo.bmp
logo_kernel.bmp
```

## 2. Boot Animation Customization

Place the boot animation you created in the specified directory.

```
(SDK)$ ls device/rockchip/common/bootshutdown/bootanimation.zip
```

Open the configuration.

```diff
(SDK)$ vim device\rockchip\common\BoardConfig.mk
-BOOT_SHUTDOWN_ANIMATION_RINGING ?= false
+BOOT_SHUTDOWN_ANIMATION_RINGING ?= true
```

## 3. Default Screen Orientation

### Default Modification of Display Orientation

```
(SDK)$ vim device/rockchip/$(dev)/BoardConfig.mk
(SDK)$ vim device/rockchip/rk356x/BoardConfig.mk // K1

    SF_PRIMARY_DISPLAY_ORIENTATION := 0 	// 0°
    SF_PRIMARY_DISPLAY_ORIENTATION := 90 	// 90°
    SF_PRIMARY_DISPLAY_ORIENTATION := 180	// 180°
    SF_PRIMARY_DISPLAY_ORIENTATION := 270	// 270°
```

> $(dev) is selected according to the motherboard CPU.
>
> K1/K1B - rk356x
>
> K3 - rk3562
>
> K8 - rk3588

### Modify Display Orientation via ADB

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

### Modify Touch Orientation

You can refer to the LCD configuration document: [Single Touch Screen Driver Configuration](..\05-Drivers_Development\LCD.md#TouchDriver)

## 4. Default Hide Status Bar

The latest firmware can be controlled by naming or sending broadcasts from an app.

Hide the status  bar via the command line.

```shell
$ am broadcast -a android.intent.action.HIDE_STATUSBAR_BAR
```

Show the status bar via the command line.

```shell
$ am broadcast -a android.intent.action.SHOW_STATUSBAR_BAR
```

The APK for testing app broadcasts is located under the network drive:

```
rk356x_data\3-SoftwareData Software Materials\Navigation-Status-test\
```

To modify the default hiding of the status bar in Android 13:

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

To modify the default prohibition of status bar pull-down in Android 13:

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

## 5. Default Hide Navigation Bar

The latest firmware can be controlled by naming or sending broadcasts from an app.

Hide the navigation bar via the command line.

```shell
$ am broadcast -a android.intent.action.HIDE_NAVIGATION_BAR
```

Show the navigation bar via the command line.

```shell
$ am broadcast -a android.intent.action.SHOW_NAVIGATION_BAR
```

The APK for testing app broadcasts is located under the network drive:

```
rk356x_data\3-SoftwareData Software Materials\Navigation-Status-test\
```

To modify the default hiding of the navigation bar in Android 13:

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

To use gesture navigation by default in Android 13:

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

## 6. Embed Third-Party APPs

Refer to `Settings2.apk`.

Place your APK and `Android.bp` in `rk-android13.0\vendor\rockchip\common\apps\Settings2`.

### Android.mk Method

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

### Android.bp Method

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

Add the compilation target to `PRODUCT_PACKAGES`.

```makefile
(SDK)$ vim vendor/rockchip/common/apps/apps.mk
PRODUCT_PACKAGES += \
       Settings2
```

## 7. Start APP Automatically on Boot

### Start the APP after Receiving the Boot Broadcast

1. Modify and add the APP's permissions and broadcast receiver in `AndroidManifest.xml`.

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

2. Add the broadcast receiver `MyReceiver.java`.

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

3. Preinstall the app to `/system/priv-app/`.

- **Method 1: Compile and preinstall into the source code. Refer to [Embed Third-Party APPs](# 6. Embed Third-Party APPs).**
- **Method 2: Preinstall via the push method.**

```
adb root; adb remount;
adb push ./app /system/priv-app/
```

## 8. Default Language

```
$ vim build/target/product/full_base.mk
    PRODUCT_LOCALES := en_US
```

| Option | Description |
| ------ | ----------- |
| en_US  | English     |
| zh_CN  | Chinese     |

## 9. Default Time Zone

[To be supplemented]

## 10. Default Never Sleep

```
$ vim device/rockchip/rk356x/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
    <integer name="def_screen_off_timeout">2147483647</integer>
```

## 11. Default Disable Lock Screen

```
$ vim frameworks/base/packages/SettingsProvider/res/values/defaults.xml
    <bool name="def_lockscreen_disabled">true</bool>
```

## 12. Sign APK

### Resign in the SDK Source Code

```shell
$ cd rk-android13.0/
$ java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" \
-jar out/host/linux-x86/framework/signapk.jar  --disable-v2 \
-w build/target/product/security/platform.x509.pem \
build/target/product/security/platform.pk8 \
old.apk new.apk
```

### Resign Using Signing-Related Files

The signing-related files required in the SDK:

```
out/host/linux-x86/lib64
out/host/linux-x86/framework/signapk.jar
build/target/product/security/platform.x509.pem
build/target/product/security/platform.pk8
```

Resign according to the provided files. Adjust the specific paths as needed.

```shell
Signing package rk-android13.0-key.tar.gz
$ tar -xvf rk-android13.0-key-20240919.tar.gz
$ ls rk-android13.0-key
$ java -Xmx2048m -Djava.library.path="rk-android13.0-key/lib64" \
-jar rk-android13.0-key/signapk.jar  --disable-v2 \
-w rk-android13.0-key/platform.x509.pem \
rk-android13.0-key/platform.pk8 \
old.apk new.apk
```

## 13. Network ADB

For network ADB, you need to start the 5555 port service via ADB first.

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

### Start Port 5555 Automatically on Boot

You need to operate via USB ADB.

```diff
(ADB)$ adb root
(ADB)$ adb remount
(ADB)$ adb pull /system/build.prop .
(ADB)$ vim build.prop
+ service.adb.tcp.port=5555
(ADB)$ adb push .\build.prop /system/build.prop
```

## 14. HDMI RX APK Customization

The K8 has one HDMI RX input. Android can display the HDMI RX screen by opening the HdmiIn APK.

The path of the HdmiIn APK:

```
(SDK)$ packages/apps/rkCamera2/
```

Configure the HDMI in screen to be full-screen.

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

## 15. Modify Debug Port Baud Rate

### Modify the Path

```
rk-android13.0\kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-android.dtsi
```

### Modify the `rockchip,baudrate` Content

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

## Rockchip Official Documentation

Rockchip has stored official documentation in the code: `RKDocs`.

```
(SDK)$ tree -L 1 RKDocs/
RKDocs/
├── android
└── common
```