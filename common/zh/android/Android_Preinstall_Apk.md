# Android_Preinstall_Apk

**预装APK**

## **文件路径**

| 主控                        | 型号                | 编译配置路径                                   | APP预装路径                        |
| --------------------------- | ------------------- | ---------------------------------------------- | ---------------------------------- |
| A133                        | K5/K5C              | android/device/softwinner/ceres-c3/ceres_c3.mk | android/vendor/aw/homlet/prebuild/ |
| H618                        | K2B/K2C             | vendor/aw/homlet/homlet.mk                     | vendor/aw/public/prebuild/         |
| RK3562/RK3568/RK3576/RK3588 | K1/K1B/K3/K7/K7C/K8 | device/rockchip/common/device.mk               | vendor/rockchip/common/apps/       |



## **预装 TestLauncher APK 示例**

第一步，在**APP预装路径**下创建 TestLauncher 目录

第二步，存放 Android.mk 和 APK 文件。

```
$ ls TestLauncher/ 
Android.mk
TestLauncher.apk
```

第三步，在**编译配置路径**下添加的模块名称为 TestLauncher 的配置；

```diff
+PRODUCT_PACKAGES += \
+		TestLauncher
```

> 模块名称对应 mk 文件中的模块名称



**Android.mk 文件编写示例**

```
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)
LOCAL_MODULE_TAGS := optional
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
LOCAL_CERTIFICATE := platform

LOCAL_MODULE := TestLauncher
LOCAL_SRC_FILES := TestLauncher.apk

include $(BUILD_PREBUILT)
```

> `LOCAL_PATH := $(call my-dir)`：设置当前模块的路径
>
> `include $(CLEAR_VARS)`：清除之前定义的变量，避免干扰当前模块
>
> `LOCAL_MODULE_TAGS := optional`：标记为可选模块
>
> `LOCAL_MODULE_CLASS := APPS`：指定模块类别为应用
>
> `LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)`：使用标准 APK 后缀
>
> `LOCAL_CERTIFICATE := platform`：使用平台签名
>
> `LOCAL_MODULE := TestLauncher`：模块名称
>
> `LOCAL_SRC_FILES := TestLauncher.apk`：指定 APK 文件路径
>
> `include $(BUILD_PREBUILT)`：使用预编译构建方式.
>
> `LOCAL_PRIVILEGED_MODULE := true` 这会将应用安装到 `/system/priv-app` 目录，获得更多系统权限。
