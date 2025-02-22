# 11-SDK source code compilation



## build environment<a id="BuildEnv-K7"></a>

This build environment configuration for Android/Linux SDK

### Hardware requirements

| System Environment | Requirements |
| --------- | ----------------- |
| System Version | Ubuntu 22.04, 64-bit |
| Number of CPU cores | More than 4 cores |
| Memory capacity | 16GB or more |
| Hard disk capacity | 200GB or more |

### Install dependent packages

```
sudo apt update
sudo apt install autoconf bc binfmt-support bison build-essential bzip2
sudo apt install chrpath cmake cpp-aarch64-linux-gnu curl device-tree-compiler diffstat
sudo apt install expat expect expect-dev fakeroot flex
sudo apt install g++ g++-multilib gawk gcc gcc-multilib git gnupg gperf gpgv2 imagemagick
sudo apt install lib32ncurses5-dev lib32readline-dev lib32z1-dev libgmp-dev 
sudo apt install libgucharmap-2-90-dev liblz4-tool libmpc-dev
sudo apt install libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-gtk3-dev 
sudo apt install libxml2 libxml2-utils live-build lzop
sudo apt install make module-assistant ncurses-dev openjdk-8-jdk 
sudo apt install patchelf pngcrush python2 python-is-python3 python-pip
sudo apt install qemu-user-static rsync schedtool squashfs-tools ssh sudo 
sudo apt install texinfo u-boot-tools unzip
sudo apt install xsltproc yasm zip zlib1g-dev pip
sudo pip install pyelftools
```

The package name will change according to the UBUNTU version update
>
> The installation of different UBUNTU versions failed, you can search for the corresponding solution on the network



## Android Compiler

### SDK Introduction

Kernel version: 6.1

Android system version: 14.0



Get the SDK source code

* Network disk directory

```
1-SDK
├── Android14
    ├── rk3576-android14.0-*.md5sum
    └── rk3576-android14.0-*.tar.gz
```

> * The date is generally referred to here, and the same is true below. The actual name is subject to the online disk.

Copy the source archive to the build host directory

If the compilation host is a virtual machine, it cannot be compiled in a shared directory

* MD5 verifies file integrity

```
md5sum rk3576-android14.0-*.tar.gz
```

> The judgment result is consistent with the content of rk3576-android14.0- * .md5sum, then the file is downloaded completely

* Unzip the source code compressed package

```
$ mkdir rk3576-android
$ tar -zxvf rk3576-android14.0-*.tar.gz -C rk3576-android/
$ cd rk3576-android/
$ git reset --hard
```



### Configure the environment

Prerequisites: Ensure the compilation hardware requirements and the success of the build environment (#BuildEnv-K7)

Configure the environment before compiling alone or all

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



### Compile all

```
./build.sh -AUCKu
```

> Mirror rockdev/Image- rk3576_u/update-rk3576-kickpi-k7-android-*



### Compile separately

* The following commands need to configure the environment once

Single series Uboot

```
./build.sh -Uu
```

Single series Android

```
./build.sh -Au
```

monolithic kernel

```
./build.sh -CKu
```

configuration Kernel defconfig

```
./build.sh -M
```



### Other instructions

** Firmware Description **

After complete compilation, the following file is generated:

```
(SDK PATH)/rockdev/Image-rk3576_u/
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

The printed image is' (SDK PATH)/rockdev/Image-rk3576_u/update-rk3576-kickpi-k7-android-*

For details of burning, see - '10-System image burning'.



** userdebug and user compile **

The default is to compile in userdebug mode. If you need a user version image, you need to modify the corresponding compilation mk.

```diff
vim device/rockchip/rk3576/BoardConfig-rk3576-kickpi-k7.mk
-export BUILD_VARIANT=userdebug
+export BUILD_VARIANT=user
```



** Android environment configuration **

Configure the environment, you can use mm/get_build_var and other Android environment commands

```shell
source build/envsetup.sh
lunch rk3576_u-userdebug
```
> For example

```
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



### FAQ

**Android compilation error due to code location move, path error **

![image-20241125155203743](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241125155203743.png)

Data needs to be cleared

```shell
source build/envsetup.sh
lunch rk3576_u-userdebug
make clean -j32
```

recompile all



**编译空间不足**

目前默认线程 -j32，通过降低线程数进行编译

方式一： 

修改编译方式，带 -J 线程

```shell
如： 修改线程为16，-J16
./build.sh -AUCKu -J16
```

方式二：

修改默认线程

```diff
$ vim device/rockchip/common/build/rockchip/build.sh
-BUILD_JOBS=32
+BUILD_JOBS=16
```



## Linux 编译

### SDK介绍

内核版本：6.1

Debian系统版本：12

Ubuntu系统版本：2404



### 获取SDK源码

* 网盘目录

```
├── 1-SDK软件源码
│   └── Linux
│       ├── rootfs
│       │   ├── linaro-rootfs-*.img
│       │   └── ubuntu-rootfs-*.img
│       └── sdk
│           ├── rk3576-linux-*.md5sum
│           └── rk3576-linux-*.tar.gz
```

>  *这里一般指代日期，下面同，实际名称以网盘为准

* 拷贝源码压缩包到编译主机目录

  若编译主机为虚拟机，不可放在共享目录下编译

* MD5校验文件完整性

```
md5sum rk3576-linux-*.tar.gz
```

> 判断结果和 rk3576-linux-*.md5sum 内容一致，则文件下载完整

* 解压源码压缩包

```
$ mkdir rk3576-linux
$ tar -zxvf md5sum rk3576-linux-*.tar.gz -C rk3576-linux/
$ cd rk3576-linux/
$ git reset --hard
```



### 配置环境

* 前提：确保编译硬件要求，以及[编译环境搭建](#BuildEnv-K7)成功

单独编译或全部编译前先配置环境

```
$ ./build.sh lunch
Log colors: message notice warning error fatal

Log saved at /home/huangcm/A/sdk/rk3576/rk3576-linux/output/sessions/2024-12-05_16-25-44
Pick a defconfig:

1. rockchip_defconfig
2. rockchip_rk3576_kickpi_k7_buildroot_defconfig
3. rockchip_rk3576_kickpi_k7_debian_defconfig
4. rockchip_rk3576_kickpi_k7_ubuntu_defconfig
Which would you like? [1]:
```

> 根据需要选择对应的主板和系统
>
> deiabn 默认使用 debian/linaro-rootfs.img 镜像，若不存在则会进行编译制作生成
>
> ubuntu 默认使用 ubuntu/ubuntu2204-rootfs.img 镜像，若不存在则会进行编译制作生成
>
> 若需要快速编译debian，可使用网盘中的 linaro-rootfs-*.img，重命名存放到 debian/linaro-rootfs.img
>
> 若需要快速编译ubuntu，可使用网盘中的 ubuntu2404-rootfs-*.img，重命名存放到 ubuntu/ubuntu-rootfs.img



### 全部编译

```
$ ./build.sh 
```



### 单独编译

**配置 Kernel defconfig**

```
./build.sh kernel-config
```



**kernel 编译**

```
./build.sh kernel
```

或

```
export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
make ARCH=arm64 rockchip_linux_defconfig rk3576.config
make ARCH=arm64 rk3576-kickpi-k7-linux.img -j24
```



**Debian 编译**

配置环境

```
sudo dpkg -i debian/ubuntu-build-service/packages/*
sudo apt-get install -f
```

编译

```
./build.sh debian
或
参考 debian/readme.md
```



**Ubuntu 编译**

配置环境

```
sudo apt-get install binfmt-support qemu-user-static --reinstall
```

编译

```
./build.sh ubuntu
或
参考 ubuntu/readme.md
```



### 其他说明

**固件说明**

完整编译后会生成如下文件：

```
rockdev
├── boot.img
├── MiniLoaderAll.bin
├── misc.img
├── oem.img 
├── parameter.txt 
├── recovery.img 
├── rootfs.img 
├── uboot.img 
├── update.img 
├── update-rk3576-kickpi-k7-linux-*-*.img 
└── userdata.img 
```

烧写的镜像为 `(源码)/rockdev/update-rk3576-kickpi-k7-linux-*-*.img `

烧录详见 - `10-系统镜像烧录`



dts path

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-linux.dts
```

defconfig path

```
kernel-6.1/arch/arm64/configs/rockchip_linux_defconfig
```



## Documentation reference

There are RK official guidance documents under the source code.

```
Android:
	（Android source code）/RKDocs/
Linux:
	（Linux source code）/docs/
```



Priority is given to viewing and carefully understanding the RK-related SDK guidance instructions

```
Android:
	（Android source code）/RKDocs/android/RK3576_Developer_Guide_Android14_SDK_CN.pdf
Linux:
	（Linux source code）/docs/readme_cn.md
	（Linux source code）/docs/cn/Rockchip_Developer_Guide_Linux_Software_CN.pdf
```

