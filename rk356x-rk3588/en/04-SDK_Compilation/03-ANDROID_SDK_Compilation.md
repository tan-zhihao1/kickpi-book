# 03-ANDROID_SDK_Compilation

### SDK Introduction

Kernel version: 5.10

Android system version: 13.0

### Obtain the SDK Source Code

* Network disk directory
```
1-SDK Source Code\rk356x-android13\
    rk356x-android13-*.tar.gz
```

* Copy the source code compressed package to the compilation host directory
If the compilation host is a virtual machine, do not place it in the shared directory for compilation.

* Verify the integrity of the file using MD5
```
$ md5sum -c *.md5
```

* Extract the source code compressed package
```
$ tar -zxvf rk356x-android13-*.tar.gz
$ cd rk356x-android13-*
$ git reset --hard
```

### Compile the Complete Image
Select the corresponding compilation command according to the CPU model. If the compilation fails, you can try to solve it yourself or contact the technical support team.

**Configure the SDK compilation items**
```
$ ./build.sh lunch

    will lunch sdk

    You're building on Linux
    Lunch menu...pick a combo:

    1. rk356x
    2. rk3588
    Which would you like? [0]:

    You're building on Linux
    Lunch menu...pick a combo:

    1. BoardConfig.mk
    2. BoardConfig-rk3566-kickpi-tx66.mk
    3. BoardConfig-rk3568-kickpi-k1a.mk
    4. BoardConfig-rk3568-kickpi-k1b.mk
```

> After the configuration is selected, the software will be configured for the selected device. If you need to switch devices, you need to reconfigure; otherwise, there is no need to configure multiple times.

**Compile the complete image**
```
$ ./build.sh -AUCKu
```

> Image generation directory: rockdev/Image-*/update-*.img

You can configure the compilation thread option and specify 32 threads.
```
$ ./build.sh -AUCKu -J32
```

> Note: If the compilation fails when building Android without specific error messages, it is likely that the virtual machine configuration is too low. You can modify the default configuration in the build.sh file and try again.
```diff
$ vim build.sh
-BUILD_JOBS=16
+BUILD_JOBS=1
$ ./build.sh -AUCKu
```

### Compile Partition Images Individually

**Compile Uboot individually**
```
$ ./build.sh -U
```

> Image generation directory: rockdev/Image-*/uboot.img

**Compile the Kernel individually**
```
$ ./build.sh -CK
```

> Image generation directory: rockdev/Image-*/boot.img

**Compile Android individually**
```
$ ./build.sh -A
```

> Image generation directory: rockdev/Image-*/super.img

**Configure the Android environment**
Configure the command environment so that you can use commands such as mm / get_build_var in the Android environment.
```shell
(SDK)$ source build/envsetup.sh
```

Select the device configuration
```
$ lunch
61
```

> Select the corresponding device according to the actual situation. For example, 'K1' and 'K1B' are for rk3568.

![2a41026b5899c13dcc725f59858abd7](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/2a41026b5899c13dcc725f59858abd7.png)

Load the corresponding environment script
```
source device/rockchip/rk356x/BoardConfig-rk3568-kickpi-k1.mk 
```

After the environment is successfully configured, you can use relevant Android commands, such as
```
$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
true
```

### Other Commonly Used Commands

**Configure the Kernel**
```
$ ./build.sh -CKM
```

**Compile the multi-screen adaptive image**
```
$ ./build.sh -AUCKum
```

> The factory Android/Linux image is a multi-screen adaptive image by default, which is compatible with the official 7-inch/10-inch/15-inch displays. It can be directly lit up when connected to any display.

## Common Issues
The following lists various common issues encountered during the SDK compilation process, along with their solutions or ideas for resolution.

### Insufficient Configuration
Common log errors due to insufficient memory configuration

* **Killed** appears
```
[hardware/rockchip/libgralloc/bifrost frameworks/native/include system/core/libs
ync system/core/libsync/include external/libdrm/include/drm] 33
Optee Version: v2
librknnrt disabled on RK3562
librknnrt disabled on RK3562
librknnrt disabled on RK3562
libeptz disabled on RK3562
libeptz disabled on RK3562
libeptz disabled on RK3562
[] 33
librockx disabled on RK3562
librockx disabled on RK3562
librockx disabled on RK3562
rk3562 WILL COMPILE rkaiq_tool_server binary
Killed
13:30:35 soong bootstrap failed with: exit status 1
ninja: build stopped: subcommand failed.

#### failed to build some targets (01:00:21 (hh:mm:ss)) ####

Build android failed!
```

* Solution:
Increase the actual memory. Generally, 32G of memory is recommended for Android. If you cannot increase the actual memory, you can try to increase the size of the swap partition and use single-threaded compilation.
Configure the thread option and specify single-threaded compilation.
```
$ ./build.sh -AUCKu -J1
```