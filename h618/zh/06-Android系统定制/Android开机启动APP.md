# Android开机启动APP



## 属性配置方式

属性配置方式是KICKPI定制系统功能，非原生系统功能。（若无法正常使用，需要咨询技术支持）

**启动原理**

`am start` 是 Android 系统中用于启动 Activity 的命令行工具，属于 Activity Manager（`am`）的一部分。它允许你通过命令行直接启动应用的特定界面，常用于测试、自动化脚本或调试场景。

示例：

手动启动设置APP

```
$ adb shell am start com.android.settings/com.android.settings.Settings
```

> com.android.settings 为APP的包名
>
> com.android.settings.Settings 为APP界面的类名



**ADB方式获取APP的包名和界面类名**

查看已安装应用

  ```
  $ adb shell pm list packages
  ```

查看应用的 Activity 信息

```
$ adb shell dumpsys package com.android.settings | grep -i activity
```



**ADB 方式配置**

```
$ adb shell
$ setprop persist.sys.bootAppPack $packname
$ setprop persist.sys.bootAppClass $classname
```

> 将属性配置为需要开机启动的APP包名和类名。

示例：

```
$ adb shell
$ setprop persist.sys.bootAppPack com.android.settings
$ setprop persist.sys.bootAppClass com.android.settings.Settings
```

> 配置完成后，开机都会启动设置



**SDK 方式配置**



添加的APP名为 TestLauncher，修改所有 TvLauncher 和 Launcher3 修改为 TestLauncher 的编译。

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
-       Launcher3 \
+		TestLauncher \
        Settings \
        SystemUI
```



## **Launcher方式**

Android 开机会默认启动 HOME 属性的APP，下面介绍修改APP为带 HOME 属性的APP。

**APP 修改**

在APP 的 AndroidManifest.xml 中添加两个 category ， 重新编译生成带 HOME 属性的APP。

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```



**ADB 修改方式**

> 当安装好新的 Laucher APP，系统会存在多个HOME属性APP，开机会让你选择其中一个作为桌面启动。
>
> 若需要只启动一个APP，需要将原本的桌面APP删除。

卸载 tv 桌面app

``` 
pm uninstall --user 0 com.android.tv.launcher
```

卸载 tablet 桌面app

```
```



SDK 修改方式

```

```



```

```



## 后台调用方式

此方式通过接收开机完成广播调起APK，可返回原本桌面

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

h618 预装到 priv-app 由于权限限制会出现APP问题，需要针对添加权限， 参考 [APP特殊权限问题](## APP特殊权限问题)



