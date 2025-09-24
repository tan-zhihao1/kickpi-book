# 01-Android常用系统定制



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 系统平台

```
(SDK)$ ./build.sh lunch 
(SDK)$ source device/rockchip/.BoardConfig.mk
(SDK)$ source build/envsetup.sh 
(SDK)$ lunch $TARGET_PRODUCT-$BUILD_VARIANT
(SDK)$ get_build_var TARGET_BOARD_PLATFORM_PRODUCT
tablet
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

> 系统自动旋转保持关闭

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

最新的固件可以命令或者app发送广播控制

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

最新的固件可以命令或者app发送广播控制

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



## 开机LOGO与动画

定制开机LOGO和动画，查看 [Android_Logo_Bootanimation](../../../common/zh/android/Android_Logo_Bootanimation.md) 文档。

```
kickpi-book/common/en/android/Android_Logo_Bootanimation.md
kickpi-book/common/zh/android/Android_Logo_Bootanimation.md
```



## 开机启动APP

如果需要开机启动APP，查看 [Android_boot_App](../../../common/zh/android/Android_boot_App.md) 文档。

```
kickpi-book/common/en/android/Android_boot_App.md
kickpi-book/common/zh/android/Android_boot_App.md
```



## APK预装



## APK签名

如果需要给APK签名，查看 [Android_Apk_Signing](../../../common/zh/android/Android_Apk_Signing.md) 文档。

```
kickpi-book/common/en/android/Android_Apk_Signing.md
kickpi-book/common/zh/android/Android_Apk_Signing.md
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

如果需要给APK签名，查看 [Android_Apk_Signing](../../../common/zh/android/Android_Apk_Signing.md) 文档。

```
kickpi-book/common/en/android/Android_Apk_Signing.md
kickpi-book/common/zh/android/Android_Apk_Signing.md
```



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

