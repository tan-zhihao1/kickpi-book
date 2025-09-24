# Android_Launcher



## A133 桌面定制

源码路径

```
SDK$ ls android/packages/apps/Launcher3/
```

单编APK

```
SDK$ cd android/
SDK$ . build/envsetup.sh
SDK$ lunch ceres_c3-userdebug
SDK$ BUILD_NUMBER=ido-a133 m Launcher3QuickStepGo -j32
```

**关闭搜索框**

```diff
--- a/android/packages/apps/Launcher3/src/com/android/launcher3/config/BaseFlags.java
+++ b/android/packages/apps/Launcher3/src/com/android/launcher3/config/BaseFlags.java
@@ -68,7 +68,7 @@ abstract class BaseFlags {
     public static final boolean LAUNCHER3_PROMISE_APPS_IN_ALL_APPS = false;
 
     // Enable moving the QSB on the 0th screen of the workspace
-    public static final boolean QSB_ON_FIRST_SCREEN = true;
+    public static final boolean QSB_ON_FIRST_SCREEN = false;
 
     public static final TogglableFlag EXAMPLE_FLAG = new TogglableFlag("EXAMPLE_FLAG", true,
             "An example flag that doesn't do anything. Useful for testing");
```

