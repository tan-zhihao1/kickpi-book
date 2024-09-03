# 22-Android12.0 Tablet系统定制

## Tablet 桌面



## 开机动画、LOGO、音频



## 内置第三方 APP





## 开机自启动 APP

要让 APP 作为 Launcher 开机自启动，需要在 AndroidManifest.xml 中添加两个 category

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```

