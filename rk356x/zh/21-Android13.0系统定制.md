# 30-Android13.0系统定制



## 1. 开机LOGO定制

开机LOGO分两个部分，分别是Uboot阶段、Kernel阶段

Uboot阶段解析图片kernel-5.10/logo.bmp

Kernel阶段解析图片kernel-5.10/logo_kernel.bmp

```
$ ls kernel-5.10/
	logo.bmp
	logo_kernel.bmp
```



## 2. 开机动画定制

```
$ ls device/rockchip/common/bootanimation.zip
```



## 3. 默认屏幕方向

### 修改显示方向

```
$ vim device/rockchip/rk356x/BoardConfig.mk

	SF_PRIMARY_DISPLAY_ORIENTATION := 0
	SF_PRIMARY_DISPLAY_ORIENTATION := 90
```



### 修改触摸方向





## 4. 默认隐藏状态栏





## 5. 默认隐藏导航栏





## 6. 内置第三方APP

参考 Setting2.apk 

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

在rk-android13.0\vendor\rockchip\common\apps\apps.mk加入编译

```makefile
PRODUCT_PACKAGES += \
       test
```





## 7. 开机自启动APP

**通过接收开机广播完成后，启动APP**

1、修改增加 APP 的权限以及广播接收  AndroidManifest.xml

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

  



## 8. 默认语言

```
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```



| 选项  | 描述 |
| ----- | ---- |
| en_US | 英文 |
| zh_CN | 中文 |
|       |      |



## 9. 默认时区





## 10. 默认永不休眠

```
$ vim device/rockchip/rk356x/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<integer name="def_screen_off_timeout">2147483647</integer>
```



## 11. 默认禁止锁屏

```
$ vim frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<bool name="def_lockscreen_disabled">true</bool>
```



## 12. 签名APK



**SDK源码中进行重签**

```shell
$ cd rk-android13.0/
$ java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" \
-jar out/host/linux-x86/framework/signapk.jar  --disable-v2 \
-w build/target/product/security/platform.x509.pem \
build/target/product/security/platform.pk8 \
old.apk new.apk
```



**使用签名相关文件重签**

SDK中需要的签名相关文件

```
out/host/linux-x86/lib64	// 避免Java库问题
out/host/linux-x86/framework/signapk.jar
build/target/product/security/platform.x509.pem
build/target/product/security/platform.pk8
```



根据提供文件进行重签，具体路径自行调整

```shell
签名包 rk-android13.0-key.tar.gz
$ tar -xvf rk-android13.0-key-20240919.tar.gz
$ ls rk-android13.0-key
$ java -Xmx2048m -Djava.library.path="rk-android13.0-key/lib64" \
-jar rk-android13.0-key/signapk.jar  --disable-v2 \
-w rk-android13.0-key/platform.x509.pem \
rk-android13.0-key/platform.pk8 \
old.apk new.apk
```



## 13.网络ADB

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

