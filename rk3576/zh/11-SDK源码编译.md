# SDK源码编译

[TOC]

## SDK下载

网盘链接



## 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

### 硬件要求

| 系统环境  | 要求            |
| --------- | --------------- |
| 系统版本  | 推荐Ubuntu18.04 |
| CPU核心数 | 4核以上         |
| 内存容量  | 16GB以上        |
| 硬盘容量  | 20GB以上        |

### 安装依赖软件包

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler 
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo apt install cmake ssh make gcc module-assistant g++ texinfo fakeroot unzip autoconf
$ sudo pip install pyelftools
```

> 软件包名称 会根据UBUNTU版本更新而变化
>
> 不同UBUNTU版本安装失败，可网络搜索对应的解决方法



## Android 编译

### 配置环境

单独编译或全部编译前先配置环境

```
$ ./build.sh lunch
will lunch sdk

You're building on Linux
Lunch menu...pick a combo:

1. rk3576
Which would you like? [0]: 1

You're building on Linux
Lunch menu...pick a combo:

1. BoardConfig.mk
2. BoardConfig-rk3576-kickpi-k7.mk
Which would you like? [0]: 2
switching to board: /home/huangcm/A/sdk/rk3576-android14.0/device/rockchip/rk3576/BoardConfig-rk3576-kickpi-k7.mk
```



### 全部编译

```
./build.sh -AUCKu
```



### 单独编译

单编安卓

```
./build.sh -Au
```



单编kernel

```
./build.sh -ACKu
```



**固件说明**

完整编译后会生成如下文件：

```
(源码)/rockdev/Image-rk3576_u/
├── boot-debug.img
├── boot.img
├── config.cfg
├── dtbo.img
├── MiniLoaderAll.bin
├── misc.img
├── parameter.txt
├── pcba_small_misc.img
├── pcba_whole_misc.img
├── recovery.img
├── resource.img
├── super.img
├── uboot.img
├── update.img
└── vbmeta.img
```

烧写的镜像为 `(源码)/rockdev/Image-rk3576_u/update.img`

烧录详见 - 11- 系统镜像烧录



**userdebug 和 user 编译**

默认为userdebug模式编译，如果需要user版本镜像则需要修改对应编译mk

```diff
vim device/rockchip/rk3576/BoardConfig-rk3576-kickpi-k7.mk
-export BUILD_VARIANT=userdebug
+export BUILD_VARIANT=user
```



**android 环境配置**

配置环境，可以使用 mm / get_build_var 等安卓环境下命令

```
source build/envsetup.sh
lunch rk3576_u-userdebug

$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
```



dts 路径

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android.dts
```



## 文档参考

源码下有 RK 官方的指导文件

```
（源码）/RKDocs/
```



优先推荐查看，仔细的了解 RK Android14 SDK 指导说明

```
（源码）/RKDocs/android/RK3576_Developer_Guide_Android14_SDK_CN.pdf
```

