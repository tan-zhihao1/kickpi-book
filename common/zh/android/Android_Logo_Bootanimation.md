# Android_Logo_Bootanimation



## 开机LOGO

LOGO替换路径如下

| 主控                 | 型号         | LOGO路径                                                     |
| -------------------- | ------------ | ------------------------------------------------------------ |
| A133                 | K5/K5C       | longan/device/config/chips/a133/configs/c3/android/bootlogo.bmp |
| H618                 | K2B/K2C      | longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp |
| RK3562/RK3568/RK3588 | K1/K1B/K3/K8 | kernel-5.10/logo.bmp<br />kernel-5.10/logo_kernel.bmp        |
| RK3576               | K7/K7C       | kernel-6.1/logo.bmp<br />kernel-6.1/logo_kernel.bmp          |

>rockchip 平台区分 Uboot、Kernel两个阶段显示LOGO，logo.bmp 为Uboot阶段，logo_kernel.bmp为Kernel阶段；
>
>LOGO的分辨率必须小于显示屏的分辨率！！！



## 开机动画

| 主控                        | 型号                | 开机动画路径                                                 |
| --------------------------- | ------------------- | ------------------------------------------------------------ |
| A133                        | K5/K5C              | android/device/softwinner/ceres-c3/media/bootanimation.zip   |
| H618                        | K2B/K2C             | device/softwinner/apollo/common/media/bootanimation/bootanimation.zip<br/>device/softwinner/apollo/common/media/bootanimation/bootanimation.mp4 |
| RK3562/RK3568/RK3576/RK3588 | K1/K1B/K3/K7/K7C/K8 | device/rockchip/common/bootshutdown/bootanimation.zip        |

Android 支持 bootanimation.mp4 以及 bootanimation.zip 两种文件格式的开机动画

> 已知问题：mp4格式开机动画 系统第一次上电启动会显示安卓开机动画
>
> 两种文件同时存在播放 mp4 优先级比 zip 高 ，具体优先级见 frameworks/base/cmds/bootanimation/BootAnimation.cpp



### A133 开机动画

第一步，替换源码动画；

动画源码路径如下

```
android/device/softwinner/ceres-c3/media/bootanimation.zip
```

第二步，确认动画拷贝方式；

bootanimation.zip 格式

```diff
$ vim android/device/softwinner/ceres-c3/ceres_c3.mk
PRODUCT_COPY_FILES += \
-		$(LOCAL_PATH)/media/bootanimation.mp4:system/media/bootanimation.mp4
+     	$(LOCAL_PATH)/media/bootanimation.zip:system/media/bootanimation.zip
```

bootanimation.mp4 格式

```diff
$ vim android/device/softwinner/ceres-c3/ceres_c3.mk
PRODUCT_COPY_FILES += \
-     	$(LOCAL_PATH)/media/bootanimation.zip:system/media/bootanimation.zip
+		$(LOCAL_PATH)/media/bootanimation.mp4:system/media/bootanimation.mp4
```



### H618 开机动画

第一步，存放动画文件（zip 或 mp4 格式）

```
device/softwinner/apollo/common/media/bootanimation/bootanimation.zip
device/softwinner/apollo/common/media/bootanimation/bootanimation.mp4
```

第二步，修改编译配置

bootanimation.zip 格式

```diff
SDK$ vim device/softwinner/apollo/common/media/config.mk
PRODUCT_COPY_FILES += \
-		$(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
+     	$(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
```

bootanimation.mp4 格式

```diff
SDK$ vim device/softwinner/apollo/common/media/config.mk
PRODUCT_COPY_FILES += \
-		$(BOOTANIMATION_CONFIG_PATH)/bootanimation.zip:system/media/bootanimation.zip
+    	$(BOOTANIMATION_CONFIG_PATH)/bootanimation.mp4:system/media/bootanimation.mp4
```



### RK3562/RK3568/RK3576/RK3588 开关机动画

开机动画放到指定目录

```
(SDK)$ ls device/rockchip/common/bootshutdown/bootanimation.zip
```

关机动画存放指定目录

```
(SDK)$ ls device/rockchip/common/bootshutdown/shutdownanimation.zip
```

打开配置

```diff
(SDK)$ vim device/rockchip/common/BoardConfig.mk
-BOOT_SHUTDOWN_ANIMATION_RINGING ?= false
+BOOT_SHUTDOWN_ANIMATION_RINGING ?= true
```

动画拷贝实现

```
(SDK)$ cat device/rockchip/common/bootshutdown/bootshutdown.mk
CUR_PATH := device/rockchip/common/bootshutdown

HAVE_BOOT_ANIMATION := $(shell test -f $(CUR_PATH)/bootanimation.zip && echo yes)
HAVE_SHUTDOWN_ANIMATION := $(shell test -f $(CUR_PATH)/shutdownanimation.zip && echo yes)

ifeq ($(HAVE_BOOT_ANIMATION), yes)
PRODUCT_COPY_FILES += $(CUR_PATH)/bootanimation.zip:$(TARGET_COPY_OUT_SYSTEM)/media/bootanimation.zip
endif
ifeq ($(HAVE_SHUTDOWN_ANIMATION), yes)
PRODUCT_COPY_FILES += $(CUR_PATH)/shutdownanimation.zip:$(TARGET_COPY_OUT_SYSTEM)/media/shutdownanimation.zip
endif
```

