# 22-Android12.0 Tablet系统定制

## 开机动画、LOGO、音频



## 内置第三方APP



## 开机自启动APP

要让APP作为Launcher开机自启动，需要在Manifest中添加两个category：

```xml
<activity android:name=".MainActivity">
    <intent-filter>
		<category android:name="android.intent.category.HOME"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```

