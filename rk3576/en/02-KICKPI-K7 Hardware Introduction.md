# 02-KICKPI-K7 Hardware Introduction



## 1. Main Control Information

![image-20250110141815408](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250110141815408.png)



## 2. Development Board Information



## 3. Cloud Disk Materials

KICKPI 开发资料通过网盘方式推送，同时支持 百度网盘、OneDrive网盘

两份网盘资料内容一致，可根据实际情况自行选择

网盘链接可通过 购买平台或QQ 咨询客服获取



* 网盘目录说明

```
├── 0-Specifications规格书
│   └── KICKPI K7 产品规格书.pdf
├── 1-SDK软件源码
│   ├── Android14
│   │   ├── rk3576-android14.0-20250217.md5sum
│   │   └── rk3576-android14.0-20250217.tar.gz
│   └── Linux
│       ├── rootfs
│       │   ├── linaro-rootfs-20250210.img
│       │   └── ubuntu-rootfs-20250217.img
│       └── sdk
│           ├── rk3576-linux-20250217.md5sum
│           └── rk3576-linux-20250217.tar.gz
├── 2-Image镜像
│   ├── Android14
│   │   └── update-rk3576-kickpi-k7-android-202502111421.img
│   ├── Debian12
│   │   └── update-rk3576-kickpi-k7-linux-debian-20250217-171230.img
│   ├── Ubuntu2404
│   │   └── update-rk3576-kickpi-k7-linux-ubuntu-20250217-160948.img
│   └── UPDATE_LOG.txt
├── 3-SoftwareData软件资料
│   ├── readme.txt
│   ├── rknn_yolov5_demo_Linux_rk3576.zip
│   └── yolov5_test.h264
├── 4-HardwareData硬件资料
│   └── K7_V1.1_20241211_SCH.pdf
├── 5-DevelopmentTool开发工具
│   ├── ADB Scrcpy 投屏
│   │   └── QtScrcpy-win-x64-v2.1.2.zip
│   ├── ADB Tool
│   │   └── adb-tools.rar
│   ├── FactoryTool 工厂镜像烧录工具
│   │   └── FactoryTool_v1.89.zip
│   ├── UART DebugTool 串口调试工具
│   │   └── MobaXterm_Portable_v20.3.zip
│   ├── USB Driver USB驱动
│   │   └── DriverAssitant_v5.13.zip
│   └── USB ImageBurnTool 镜像烧录工具
│       └── RKDevTool_v3.30_for_window.zip
└── 6-Docment文档
    └── readme.txt
```

0-Specifications规格书：

通过规格书可以快速了解产品的特性和性能。

1-SDK软件源码：

通过软件源码可以编译出主板镜像，定制系统软件。

Android14/ - 安卓源码，可编译主板Android 14.0镜像

Linux/rootfs/ - Debian12和Ubuntu2404 备份的文件系统

Linux/sdk/ - Linux源码，可编译主板Debian12以及Ubuntu2404 镜像

2-Image镜像：

支持 Android14 / Debian12 / Ubuntu2404 系统镜像

通过烧录镜像更新主板的系统，说明见[系统镜像烧录](10-系统镜像烧录.md)

3-SoftwareData软件资料：

软件相关的资料，如程序例子、软件补丁等。

4-HardwareData硬件资料：

硬件相关的资料，如原理图等。

5-DevelopmentTool开发工具：

烧录调试相关工具，如烧录软件、ADB软件等。

6-Docment文档：

用户指导、开发相关资料文档。



