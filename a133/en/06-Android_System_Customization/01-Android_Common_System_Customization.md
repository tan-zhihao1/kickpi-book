# 01-Android_Common_System_Customization

H618 Android 12.0 is an official TV system compatible with TV-version apps. Installing tablet or phone apps may cause compatibility issues.

---

## TV Launcher

Enable the corresponding `PRODUCT_PACKAGES` for the TV launcher:  
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
+PRODUCT_PACKAGES += \
       Launcher3 \
+#PRODUCT_PACKAGES += \
       Launcher3 \
        Settings \
        SystemUI
```

**APK Path**:  
```
vendor/aw/homlet/package/TVLauncher
```

---

## Tablet Launcher

Enable the corresponding `PRODUCT_PACKAGES` for the tablet launcher:  
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

**APK Path**:  
```
packages/apps/Launcher3/
```

---

## APP/File Compilation Issues

Android uses lazy compilation. Directly replacing files may not trigger recompilation. Solutions:  
1. `make installclean`  
2. Delete specific files:  
   ```
   rm out/target/product/apollo-p2/xxx/xxx
   ```
3. Modify compilation conditions (e.g., filenames or APK names).  

---

## Default Screen Orientation

Modify the default display orientation by editing:  
```
android/device/softwinner/ceres-c3/ceres_c3.mk
```
Change:  
```diff
# Set primary display orientation to 270
PRODUCT_PROPERTY_OVERRIDES += \
    ro.surface_flinger.primary_display_orientation=ORIENTATION_0
    # Modify to:
    # ro.surface_flinger.primary_display_orientation=ORIENTATION_90
    # ro.surface_flinger.primary_display_orientation=ORIENTATION_270
```

---

## Boot Logo

**Logo Replacement Path**:  
```
longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp
```

---

## Boot Animation

Supported formats: `bootanimation.mp4` (higher priority) or `bootanimation.zip`.  

**Modification Path**:  
```
device/softwinner/apollo/common/media/config.mk
```

**Example Configuration**:  
- For `bootanimation.zip`:  
  ```makefile
  PRODUCT_COPY_FILES += \
      $(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
  ```
- For `bootanimation.mp4`:  
  ```makefile
  PRODUCT_COPY_FILES += \
      $(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
  ```

> Note: The first boot after flashing will show the default Android animation. Reboot to apply the custom animation.

---

## Default Language

**Method 1**: Modify `persist.sys.locale` in:  
```
device/softwinner/apollo/apollo_p2.mk
```
Example:  
```diff
PRODUCT_PROPERTY_OVERRIDES += \
    persist.sys.locale=zh-CN
```

**Method 2**: Adjust `persist.sys.country` and `persist.sys.language`:  
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

**Method 3**: Set `PRODUCT_LOCALES` in:  
```makefile
build/target/product/full_base.mk
```
Options:  
| Value  | Description |  
|--------|-------------|  
| `en_US` | English     |  
| `zh_CN` | Chinese     |  

---

## Pre-installing Third-party Apps

**Steps**:  
1. Place your APK and `Android.bp`/`Android.mk` in:  
   ```
   h618-android12.0/vendor/aw/public/prebuild/apk
   ```
2. Example `Android.mk`:  
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
3. Example `Android.bp`:  
   ```makefile
   android_app_import {
       name: "test",
       apk: "test.apk",
       certificate: "platform",
       dex_preopt: { enabled: true },
       privileged: true,
       enforce_uses_libs: false,
   }
   ```
4. Add the app to `vendor/aw/homlet/homlet.mk`:  
   ```makefile
   PRODUCT_PACKAGES += \
          test
   ```

---

## Auto-start APP on Boot

### **Launcher Method**  
Add the following to `AndroidManifest.xml`:  
```xml
<activity android:name=".MainActivity">
    <intent-filter>
        <category android:name="android.intent.category.HOME"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```
Override existing launchers using:  
- **Android.bp**:  
  ```makefile
  overrides: ["Launcher2", "Launcher3", ...]
  ```
- **Android.mk**:  
  ```makefile
  LOCAL_OVERRIDES_PACKAGES := Launcher2 Launcher3 ...
  ```

### **Background Broadcast Method**  
1. Add permissions and a broadcast receiver to `AndroidManifest.xml`:  
   ```xml
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
   <receiver
       android:name=".MyReceiver"
       android:enabled="true"
       android:exported="true">
       <intent-filter android:priority="1000">
           <action android:name="android.intent.action.BOOT_COMPLETED" />
       </intent-filter>
   </receiver>
   ```
2. Implement the receiver:  
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
3. Pre-install the app to `/system/priv-app/` (see [Pre-installing Third-party Apps](#pre-installing-third-party-apps)).  

---

## APK Push to `priv-app`

```bash
adb root
adb remount
adb push <APK_PATH> /system/priv-app/
adb reboot
```

---

## APP Signature Files

**Signature Paths**:  
```
build/target/product/security/platform.pk8
build/target/product/security/platform.x509.pem
out/host/linux-x86/framework/signapk.jar
```

---

## Special Permission Issues

If privileged permissions are missing, modify:  
```
frameworks/base/data/etc/privapp-permissions-platform.xml
```
Example fix:  
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

---

## Full Rebuild Not Effective

Clean before rebuilding:  
```bash
source build/envsetup.sh
lunch apollo_p2-userdebug
make installclean -j32
./build.sh
```

---

## HDMI Fixed Resolution

**HDMI TX Attributes**:  
```
/sys/class/hdmi/hdmi/attr
```

---

## ADB Functions

### **USB ADB Connection**  
1. Connect the device via USB.  
2. Install drivers if unrecognized (see screenshots below).  
3. Run `adb shell` to connect.  

### **Network ADB**  
1. Enable TCP/IP mode:  
   ```bash
   adb tcpip 5555
   adb connect <DEVICE_IP>
   ```
2. Auto-start TCP/IP on boot:  
   ```bash
   adb root
   adb remount
   adb pull /system/build.prop
   # Add: service.adb.tcp.port=5555
   adb push build.prop /system/build.prop
   adb reboot
   ```

### **ADB Screen Casting**  
Use tools from:  
```
h618_data\5-DevelopmentTools\ADB Screen Cast
```

---

## Screen Rotation

Use ADB commands:  
```bash
# 0°
adb shell settings put system user_rotation 0
# 90°
adb shell settings put system user_rotation 1
# 180°
adb shell settings put system user_rotation 2
# 270°
adb shell settings put system user_rotation 3
```