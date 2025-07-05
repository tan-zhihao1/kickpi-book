# Android开机启动APP



## KICKPI 属性启动APP

此方法通过 SystemUI 服务后台启动指定的应用，在开机完成后。

启动原理：

```
am start com.android.settings/com.android.settings.Settings
```



**ADB 方式配置**

```
$ adb shell
$ setprop persist.sys.bootAppPack $packname
$ setprop persist.sys.bootAppClass $classname
```

示例：

```
$ adb shell
$ setprop persist.sys.bootAppPack com.android.settings
$ setprop persist.sys.bootAppClass com.android.settings.Settings
```

> com.android.settings 为设置应用的包名
>
> com.android.settings.Settings

SDK 方式配置





## **Launcher方式启动**

此方式替换桌面，返回 HOME 为 替换后的APP

要让 APP 作为 Launcher 开机自启动，需要在 AndroidManifest.xml 中添加两个 category

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```



h618 作为Launcher 由于权限限制会出现APP问题，需要针对添加权限， 参考 [APP特殊权限问题](## APP特殊权限问题)

没有桌面属性缺失问题，需要添加 上面两个 category

![image-20241101094329876](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241101094329876.png)



预置时替换原生桌面，需要添加覆盖属性

Android.bp 方式

#overrides覆盖应用列表
#覆盖其他应用：overrides 列表中的每个条目都是一个应用的名称，这些应用如果存在，将会被当前应用覆盖。这意味着如果设备上已经安装了列表中的任何一个启动器应用，"LauncherJingWei" 将会优先启动并接管启动器的功能。
#优先级：通过这种方式，你可以确保你的应用在某些情况下优先于其他应用被加载和使用。这对于预装应用特别有用，因为它们通常需要替代系统默认的应用。

```
   overrides: [
        "Launcher2",
        "Launcher3",
        "Launcher3QuickStep",
        "Launcher3QuickStepGo",
    ],
```

Android.mk 方式

```
LOCAL_OVERRIDES_PACKAGES := Launcher2 Launcher3 Launcher3QuickStep Launcher3QuickStepGo
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



