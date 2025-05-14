# 03-Android_SDK_Compilation

### SDK Introduction

Kernel version: 6.1

Android system version: 14.0


### Obtain the SDK Source Code

* Network disk directory

```
1-SDK Software Source Code
├── Android14
    ├── rk3576-android14.0-*.md5sum
    └── rk3576-android14.0-*.tar.gz
```

> * Here, * generally refers to the date. The same applies below. The actual names shall prevail in the network disk.

* Copy the source code compressed package to the compilation host directory

  If the compilation host is a virtual machine, do not compile in the shared directory.

* MD5 verification of file integrity

```
md5sum rk3576-android14.0-*.tar.gz
```

> If the result is consistent with the content of rk3576-android14.0-*.md5sum, the file is downloaded completely.

* Unzip the source code compressed package

```
$ mkdir rk3576-android
$ tar -zxvf rk3576-android14.0-*.tar.gz -C rk3576-android/
$ cd rk3576-android/
$ git reset --hard
```


### Configure the Environment

> Prerequisite: Ensure the hardware requirements for compilation and the successful [setup of the compilation environment](01-Compilation Environment Setup.md).

Configure the environment before single compilation or full compilation.

```bash
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


### Full Compilation

> Note: Ensure the hardware requirements for compilation and the successful [setup of the compilation environment](01-Compilation Environment Setup#BuildEnv-K7).

Standard image

```
./build.sh -AUCKu
```

> Image: rockdev/Image-rk3576_u/update-rk3576-kickpi-k7-android-*.img

Multi-screen recognition image

```
$ ./build.sh -UCKAum
```


### Single Compilation

* The following commands require [configuring the environment](#androidEnv-K7) first.

Single compile Uboot

```
./build.sh -Uu
```

Single compile Android

```
./build.sh -Au
```

Single compile kernel

```
./build.sh -CKu
```

Configure Kernel defconfig

```
./build.sh -M
```


### Other Instructions

**Firmware Instructions**

The following files will be generated after a full compilation:

```
(Source code)/rockdev/Image-rk3576_u/
rockdev/Image-rk3576_u/
├── baseparameter.img
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
├── update-rk3576-kickpi-k7-android-*.img
└── vbmeta.img
```

The image to be flashed is `(Source code)/rockdev/Image-rk3576_u/update-rk3576-kickpi-k7-android-*.img`

For details on flashing, refer to - `10-System Image Flashing`


**Multi-screen Recognition Compilation**

```
$ ./build.sh -UCKAum
```


**userdebug and user Compilation**

The default compilation mode is userdebug. If you need a user version image, you need to modify the corresponding compilation mk.

```diff
vim device/rockchip/rk3576/BoardConfig-rk3576-kickpi-k7.mk
-export BUILD_VARIANT=userdebug
+export BUILD_VARIANT=user
```


**Android Environment Configuration**

Configure the environment to use commands such as mm / get_build_var in the Android environment.

```shell
// Configure the environment
source build/envsetup.sh
lunch rk3576_u-userdebug

// For example
$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
true
```

dts path

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android.dts
```

defconfig path

```
kernel-6.1/arch/arm64/configs/rockchip_defconfig
```


### Common Issues

**Android compilation error due to path errors caused by code location changes**

![image-20241125155203743](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241125155203743.png)

You need to clear the data.

```shell
source build/envsetup.sh
lunch rk3576_u-userdebug
make clean -j32
```

Perform a full compilation again.


**Insufficient compilation space**

Currently, the default thread is -j32. Compile by reducing the number of threads.

Method 1: 

Modify the compilation method and specify the thread with -J.

```shell
For example, change the thread to 16, -J16
./build.sh -AUCKu -J16
```

Method 2:

Modify the default thread.

```diff
$ vim device/rockchip/common/build/rockchip/build.sh
-BUILD_JOBS=32
+BUILD_JOBS=16
```
