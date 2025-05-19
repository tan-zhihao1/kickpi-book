# 01-Android_Common_System_Customization

## Android Environment Setup

```bash
cd android
source build/envsetup.sh
lunch ceres_c3-userdebug
```

---

## Developer Mode

**Steps to Enable**:  
`Settings -> About tablet -> Tap "Build number" 7 times -> "Developer mode activated"`  

**Developer Options Path**:  
`Settings -> System -> Developer options`

---

## APP/File Compilation Not Taking Effect

Due to Android's **lazy compilation**, directly replacing files may not trigger rebuilds. Solutions:  
1. `make installclean`  
2. `rm out/target/product/apollo-p2/xxx/xxx`  
3. Modify compilation conditions (e.g., filename or APK name changes).

---

## Screen Rotation via ADB

Use these commands to rotate the screen:  
```bash
# 0° (Default)
adb shell settings put system user_rotation 0

# 90°
adb shell settings put system user_rotation 1

# 180°
adb shell settings put system user_rotation 2

# 270°
adb shell settings put system user_rotation 3
```

---

## Boot Logo Replacement

**Path**:  
```
longan/device/config/chips/a133/configs/c3/android/bootlogo.bmp
```

> Requirements:  
> - **Format**: 32-bit BMP  
> - **Resolution**: Lower than the screen's native resolution  

---

## Boot Animation Customization

### Default Replacement

Supported formats: `bootanimation.mp4` (higher priority) or `bootanimation.zip`.  

**Default Animation Path**:  
```
android/device/softwinner/ceres-c3/media/bootanimation.zip
```

**Copy Path Configuration**:  
Modify `android/device/softwinner/ceres-c3/ceres_c3.mk`:  
```makefile
# For ZIP
PRODUCT_COPY_FILES += $(LOCAL_PATH)/media/bootanimation.zip:system/media/bootanimation.zip

# For MP4
PRODUCT_COPY_FILES += $(LOCAL_PATH)/media/bootanimation.mp4:system/media/bootanimation.mp4
```

> **Note**: MP4 animations will show the default Android logo on first boot. Reboot to apply changes.

### Live Replacement

```bash
adb root
adb remount
adb push $(bootanimation_path) /system/media/bootanimation.zip
adb reboot
```

### Creating `bootanimation.zip`

**File Structure**:  
```
part0/0000.png
part0/0001.png
...
part1/0013.png
...
desc.txt
audio_conf.txt  # Optional for sound
```

**`desc.txt` Configuration**:  
```
800 480 15   # Width, Height, FPS
p 1 0 part0  # Play part0 once
p 0 0 part1  # Loop part1 until boot completes
```

**Compression Instructions**:  
- **Windows**: Use "Store" compression level.  
- **Linux**: `zip -0qry bootanimation.zip *.txt part0/ part1/`  

---

## Boot Sound

Add `Audio.wav` to the `part0` directory and include `audio_conf.txt` in the root.  

---

## Launcher Customization

**Source Path**:  
```
android/packages/apps/Launcher3/
```

**Compile Launcher APK**:  
```bash
cd android/
. build/envsetup.sh
lunch ceres_c3-userdebug
BUILD_NUMBER=ido-a133 m Launcher3QuickStepGo -j32
```

**Disable Search Bar**:  
```diff
--- a/android/packages/apps/Launcher3/src/com/android/launcher3/config/BaseFlags.java
+++ b/android/packages/apps/Launcher3/src/com/android/launcher3/config/BaseFlags.java
@@ -68,7 +68,7 @@ abstract class BaseFlags {
     public static final boolean LAUNCHER3_PROMISE_APPS_IN_ALL_APPS = false;
 
     // Enable moving the QSB on the 0th screen of the workspace
-    public static final boolean QSB_ON_FIRST_SCREEN = true;
+    public static final boolean QSB_ON_FIRST_SCREEN = false;
```

---

## Default Language Settings

**Set via `persist.sys.locale`**:  
Modify `device/softwinner/apollo/apollo_p2.mk`:  
```makefile
PRODUCT_PROPERTY_OVERRIDES += persist.sys.locale=zh-CN
```

**Override Country/Language**:  
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

**Locale Priority**: Defined in `frameworks/base/core/jni/AndroidRuntime.cpp`.  

---

## Integrating Third-Party Apps

**Example for `test.apk`**:  

**Android.mk**:  
```makefile
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := optional
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
LOCAL_CERTIFICATE := PRESIGNED
LOCAL_MODULE := test
LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
include $(BUILD_PREBUILT)
```

**Android.bp**:  
```makefile
android_app_import {
    name: "test",
    apk: "test.apk",
    certificate: "platform",
    dex_preopt: { enabled: true },
    enforce_uses_libs: false,
}
```

**Add to Build**:  
```makefile
PRODUCT_PACKAGES += test
```

---

## Auto-Start Apps on Boot

### Method 1: Set as Launcher  
Add to `AndroidManifest.xml`:  
```xml
<activity android:name=".MainActivity">
    <intent-filter>
        <category android:name="android.intent.category.HOME"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```

### Method 2: Boot Broadcast Receiver  
**AndroidManifest.xml**:  
```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<application>
    <receiver
        android:name=".MyReceiver"
        android:enabled="true"
        android:exported="true">
        <intent-filter android:priority="1000">
            <action android:name="android.intent.action.BOOT_COMPLETED"/>
        </intent-filter>
    </receiver>
</application>
```

**Receiver Code**:  
```java
public class MyReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent.getAction().equals("android.intent.action.BOOT_COMPLETED")) {
            Intent i = new Intent(context, MainActivity.class);
            i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.startActivity(i);
        }
    }
}
```

**Preinstall to `/system/priv-app/`**:  
```bash
adb root
adb remount
adb push ./app /system/priv-app/
```

---

## ADB Screen Cast

**Tool Path**:  
`5-DevelopmentTools/ADB Screen Cast`  

![image](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250303103950558.png)  
![image](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250303104027819.png)  

---

## DPI Configuration

**Modify Default DPI**:  
```diff
--- a/android/device/softwinner/ceres-c3/ceres_c3.mk
+++ b/android/device/softwinner/ceres-c3/ceres_c3.mk
@@ -256,7 +256,7 @@ PRODUCT_SYSTEM_DEFAULT_PROPERTIES += \
 endif
 
 PRODUCT_PROPERTY_OVERRIDES += \
-    ro.sf.lcd_density=160
+    ro.sf.lcd_density=120
```

**Command Line**:  
```bash
wm density 120
```

---

## FAQ

### 1. Headphones Always Detected  
The driver defaults to headphone mode for speaker functionality. To revert:  
![Fix](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/538c0528ac148df300bf0408eada0234.jpg)  
```