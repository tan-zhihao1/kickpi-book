# 21-Android13.0 system customization



## 1. Boot LOGO customization

The boot LOGO is divided into two parts, namely the Uboot stage and the Kernel stage

Uboot stage parsing image kernel-5.10/logo.bmp

Kernel stage parsing image kernel-5.10/logo_kernel

```
$ ls kernel-5.10/
	logo.bmp
	logo_kernel.bmp
```



## 2. Boot animation customization

```
$ ls device/rockchip/common/bootanimation.zip
```



## 3. Default screen orientation

### Modify display orientation

```
$ vim device/rockchip/rk356x/BoardConfig.mk

	SF_PRIMARY_DISPLAY_ORIENTATION := 0 	//默认
	SF_PRIMARY_DISPLAY_ORIENTATION := 90 	//90°
	SF_PRIMARY_DISPLAY_ORIENTATION := 180	//180°
	SF_PRIMARY_DISPLAY_ORIENTATION := 270	//270°
```



### Modify touch direction

Take the TP with a 7-inch screen as an example, you can modify the direction you want through this.

```
$ vim kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-kickpi-lcd-lvds0-7-1024-600.dtsi
		
		gtp_change_x2y = <0>; //1 is the X-Y axis alignment
		gtp_overturn_x = <0>; //1 is the reverse of the X axis
		gtp_overturn_y = <0>; //1 is the reverse of the Y-axis.

```





## 4. Default hide status bar





## 5. Default hide navigation bar





## 6. Built-in third-party APP

Refer to Settings2.apk

Rk-android13.0\ vendor\ rockchip\ common\ apps\ Settings2 put your apk and Android.bp

Android.mk way

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
    application, which can be set
    dex_preopt: {
        enabled: true,
    },

    privileged: true, 
    //product_specific: true,
    //proprietary: true,
    enforce_uses_libs: false,
}
```

Add compilation in rk-android13.0\ vendor\ rockchip\ common\ apps\ apps.mk

```makefile
PRODUCT_PACKAGES += \
       Settings2
```





## 7. Boot the self-starting APP

**After receiving the boot broadcast, start the APP **

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



2. Increase broadcast reception MyReceiver.java

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



3. Pre-install the app to /system/priv-app/

* Method 1, compile and pre-install to the source code, refer to [Built-in third-party APP] (#6. Built-in third-party APP)

* Method 2, push method pre-installed

  ```
  adb root; adb remount;
  adb push ./app /system/priv-app/
  ```

  



## 8. Default language

```
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```



| Options | Description |
| ----- | ---- |
| en_US | English |
| zh_CN | Chinese |
| | |



## 9. Default time zone





## 10. Default never sleeps

```
$ vim device/rockchip/rk356x/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<integer name="def_screen_off_timeout">2147483647</integer>
```



## 11. Default disable lock screen

```
$ vim frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<bool name="def_lockscreen_disabled">true</bool>
```



## 12. Signature APK



**Resign in the SDK source code**

```shell
$ cd rk-android13.0/
$ java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" \
-jar out/host/linux-x86/framework/signapk.jar  --disable-v2 \
-w build/target/product/security/platform.x509.pem \
build/target/product/security/platform.pk8 \
old.apk new.apk
```



**Re-sign with signature-related documents **

Signature-related files required in the SDK

```
out/host/linux-x86/lib64	// Avoid Java library problems
out/host/linux-x86/framework/signapk.jar
build/target/product/security/platform.x509.pem
build/target/product/security/platform.pk8
```



Re-sign according to the provided documents, and adjust the specific path by yourself

```shell
signature package rk-android13.0-key.tar.gz
$ tar -xvf rk-android13.0-key-20240919.tar.gz
$ ls rk-android13.0-key
$ java -Xmx2048m -Djava.library.path="rk-android13.0-key/lib64" \
-jar rk-android13.0-key/signapk.jar  --disable-v2 \
-w rk-android13.0-key/platform.x509.pem \
rk-android13.0-key/platform.pk8 \
old.apk new.apk
```



## 13.Network ADB

The network port ADB needs to start the 5555 port service first.

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

#### When powered on, port 5555 is started by default.

It needs to be operated via USB adb.

```
 adb root
 adb remount
 adb pull /system/build.prop
```
add in build.prop
Add this line
```diff
+ service.adb.tcp.port=5555
```
substitute entry
```
 adb push .\build.prop /system/build.prop
```



## 14. HDMI in APK Customization

hdmi in APK path

```
packages/apps/rkCamera2/
```

hdmi in Full screen configuration

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



## 15. Modify the debugging port baud rate

Modify the path:

```
 rk-android13.0\kernel-5.10\arch\arm64\boot\dts\rockchip\rk3568-android.dtsi
```

Modify rockchip, baudrate content:

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

