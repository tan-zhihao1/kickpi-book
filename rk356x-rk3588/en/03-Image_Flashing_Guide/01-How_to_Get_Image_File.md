# 01-How_to_Get_Image_File

镜像文件可通过以下两种方式获取：网盘下载、SDK编译

SDK编译详细步骤参考文档 [LINUX_SDK源码编译](../04-SDK编译/02-LINUX_SDK编译.md)、[ANDROID_SDK源码编译](../04-SDK编译/03-ANDROID_SDK编译.md)。



## 网盘目录

```
2-Image/
├── RK3562-K3
│   ├── Android13.0
│   │   ├── back
│   │   │   ├── update-rk3562-kickpi-k3-android-disable-ir-2024053009.img
│   │   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2024051101.img
│   │   │   └── update-rk3562-kickpi-k3-android-test-2024052709.img
│   │   ├── UPDATE_LOG.txt
│   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2025030314.img
│   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2025031919.img
│   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2025040216.img
│   │   └── 新款7寸校准镜像
│   │       └── update-rk3562-kickpi-k3-android-multi-lcd-2025042817.img
│   ├── Debian11
│   │   ├── update_log.txt
│   │   ├── update-rk3562-kickpi-k3-linux-debian-multi-lcd--20250304-180123.img
│   │   ├── update-rk3562-kickpi-k3-linux-debian-multi-lcd--20250321-092932.img
│   │   └── update-rk3562-kickpi-k3-linux-debian-multi-lcd--20250407-201723.img
│   └── Ubuntu2004
│       ├── Update Log.txt
│       ├── update-rk3562-kickpi-k3-linux-ubuntu-multi-lcd--20250304-174704.img
│       ├── update-rk3562-kickpi-k3-linux-ubuntu-multi-lcd--20250321-091543.img
│       └── update-rk3562-kickpi-k3-linux-ubuntu-multi-lcd--20250407-151924.img
├── RK3566-TX66
│   ├── Android11-TV
│   │   └── update-rk3566-android11-2023052415.img
│   ├── Android13
│   │   └── update-rk3566-kickpi-tx66-android-2023072110.img
│   ├── Debian11
│   │   └── update-rk3566-box-tx66-debian-2023052415.img
│   └── Ubuntu2004
│       └── update-rk3566-box-tx66-ubuntu-2023052415.img
├── RK3568-K1
│   ├── Android13.0
│   │   ├── UPDATE_LOG.txt
│   │   ├── update-rk3568-kickpi-k1-android-multi-lcd-2025031920.img
│   │   └── update-rk3568-kickpi-k1-android-multi-lcd-2025040218.img
│   ├── Debian11
│   │   ├── update_log.txt
│   │   ├── update-rk3568-kickpi-k1-linux-debian-multi-lcd--20250320-155119.img
│   │   └── update-rk3568-kickpi-k1-linux-debian-multi-lcd--20250403-150845.img
│   └── Ubuntu2004
│       ├── config.cfg
│       ├── Update Log.txt
│       ├── update-rk3568-kickpi-k1-linux-ubuntu-multi-lcd--20250320-140656.img
│       └── update-rk3568-kickpi-k1-linux-ubuntu-multi-lcd---20250403-144048.img
├── RK3568-K1B
│   ├── Android13.0
│   │   ├── UPDATE_LOG.txt
│   │   ├── update-rk3568-kickpi-k1b-android-multi-lcd-2025031914.img
│   │   ├── update-rk3568-kickpi-k1b-android-multi-lcd-2025040314.img
│   │   ├── 屏幕及USB触摸向右旋转90°固件
│   │   │   └── update-rk3568-kickpi-k1b-android-2025011009.img
│   │   └── 校准新款7寸屏触摸固件
│   │       ├── README_CN.txt
│   │       ├── README_EN.txt
│   │       └── update-rk3568-kickpi-k1b-android-multi-lcd-2025040918.img
│   ├── Debian11
│   │   ├── update_log.txt
│   │   ├── update-rk3568-kickpi-k1b-linux-debian--multi-lcd--20250320-192313.img
│   │   └── update-rk3568-kickpi-k1b-linux-debian-multi-lcd--20250403-164358.img
│   └── Ubuntu2004
│       ├── Update Log.txt
│       ├── update-rk3568-kickpi-k1b-linux-ubuntu--20250207-141840.img
│       ├── update-rk3568-kickpi-k1b-linux-ubuntu-multi-lcd--20250304-153910.img
│       ├── update-rk3568-kickpi-k1b-linux-ubuntu-multi-lcd--20250320-173529.img
│       └── update-rk3568-kickpi-k1b-linux-ubuntu-multi-lcd--20250403-161034.img
└── RK3588-K8
    ├── Android13.0
    │   ├── update-rk3588-kickpi-k8-android-2025022811.img
    │   └── update-rk3588-kickpi-k8-android-multi-lcd-2025032517.img
    └── Debian11
        └── update-rk3588-kickpi-k8-linux-debian-multi-lcd-20250506.img
```



## 镜像名称解析

```
update-rk3588-kickpi-k8-android-2025022811.img
A(update)-B(rk3588-kickpi-k8)-C(android)-D(multi-lcd)-E(202504171451).img
```

**A部分**

update - 指完整镜像包。



**B部分**

主控型号、硬件版型部分。

rk3588-kickpi-k8 - 指代主控型号rk3588、硬件板型k8。



**C部分**

系统名称部分。

android - Android系统镜像

ubuntu - Ubuntu系统镜像

debian - Debian系统镜像



**D部分**

镜像名称的其他标志，用于指示镜像一些特殊的区别。

multi-lcd，默认为兼容多种屏接口

hdmi ，默认显示hdmi接口

8-MX080B2140，默认显示型号 MX080B2140屏幕



**E部分**

202504171451 - 镜像更新日期。