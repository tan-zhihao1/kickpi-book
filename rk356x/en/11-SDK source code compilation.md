# 11-SDK source code compilation






## 1. build environment

This build environment configuration for Android/Linux SDK

### Hardware requirements

| System Environment | Requirements |
| --------- | ----------- |
| System Version | Ubuntu 22.04 |
| Number of CPU cores | More than 4 cores |
| Memory capacity | 16GB or more |
| Hard disk capacity | 200GB or more |



### Install dependent packages

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev libncurses5
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev libgmp-dev libmpc-dev
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler 
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools
```

The package name will change according to the UBUNTU version update
>
> The installation of different UBUNTU versions failed, you can search for the corresponding solution on the network





## 2. Linux SDK compilation

### SDK Introduction

Rockchip Linux SDK consists of SDK source packages and file system images

SDK source package kernel version 5.10, support Ubuntu 20.04, Debian11 file system



### Get the SDK source code

**Network disk download SDK source code compressed package**

The network disk SDK directory is as follows

```
1-SDK\rk356x-linux\
	rk356x-linux-*.tar.gz
	rk356x-linux-*.md5
```



**Copy the source zip to the build host directory**

> Precautions
>
> 1. If the compilation host is a virtual machine, it cannot be compiled in a shared directory



**MD5 verifies file integrity**

```
$ md5sum -c *.md5
```



**Unzip the source code compressed package**

```
$ tar -zxvf rk356x-linux-*.tar.gz

$ ls -al
	.git
```

> The SDK source code compressed package is shared through the Git repository, and the .git folder can be viewed with the ls -al command



**Restore source files**

Enter the extracted directory and restore the source code through git.

```
$ git reset --hard
```

> Note: Please operate under Linux and git is installed
>



### Get the file system image

The SDK source package does not contain the file system image. You need to refer to the following steps to extract and copy the file system to the SDK source code separately.

The network disk provides debian and ubuntu file system images, which can be downloaded by yourself according to your needs



**File system network disk directory**

```
1-SDK\rk356x-linux\
	linaro-rootfs-20230524.tar.gz
	ubuntu-rootfs-20230523.tar.gz
```



**MD5 verifies file integrity**

```
$ md5sum -c *.md5
```



**Unzip the debian image file**

```
$ tar -zxvf linaro-rootfs-*.tar.gz
$ cp linaro-rootfs.img debian/

$ ls debian/linaro-rootfs.img
```

> The name and directory of the Debian image file must be consistent
>
> File system directory：debian/linaro-rootfs.img
>
> lLunch selects debian and will use this linaro-rootfs.img to compile 



**Unzip the Ubuntu image file**

```
$ tar -zxvf ubuntu-rootfs-*.tar.gz
$ cp ubuntu-rootfs.img ubuntu/

$ ls ubuntu/ubuntu-rootfs.img
```

The names and directories of Ubuntu image files must be consistent
>
> File system directory: ubuntu/ubuntu-rootfs.img
>
> lunch Select ubuntu and it will be compiled with this ubuntu-rootfs.img



### Compile the full image

build.sh SDK compilation for the first time, you must select the compilation configuration information, such as: CPU model, board model, file system model

For specific board model differences, please check the "Introduction to the Getting Started Document".



**SDK compilation configuration**

```
$ ./build.sh lunch
	
    You're building on Linux
    Lunch menu...pick a combo:
    1. rk356x
    2. rk3588
    Which would you like? [0]:	# Select the CPU model

    You're building on Linux
    Lunch menu...pick a combo:
	1. BoardConfig-rk3566-kickpi-tx66.mk
	2. BoardConfig-rk3568-kickpi-tx68.mk
    Which would you like? [0]:	# Select board model

    You're building on Linux
    Lunch menu...pick a combo:
    0. default RootfsConfig.mk
    1. RootfsConfig-buildroot.mk
    2. RootfsConfig-debian.mk
    3. RootfsConfig-ubuntu.mk
    Which would you like? [0]:	# Select the file system model
```

> The configuration command can be compiled and executed once for the first time.



**compilation mirroring**

```
$ ./build.sh
```

> Image generation directory: rockdev/update-\ * .img
>
> If the compilation fails, you can try to solve it yourself, or you can contact technical support customer service to solve it.



### Compile partition images separately

**Compile Uboot separately**

```
$ ./build.sh uboot
```

> Image generation directory: rockdev/uboot.img

**Compile Kernel Separately**

```
$ ./build.sh kernel
```

> Image generation directory: rockdev/boot.img

**Compile Buildroot separately**

```
$ ./build.sh buildroot
```

> Image generation directory: rockdev/rootfs.img



### Other commonly used commands

**Kernel Configuration**

```
$ ./build.sh kernel-config
```

**buildroot configuration**

```
$ ./build.sh menuconfig
```

**Compile multi-screen adaptive mirroring**

```
$ ./build.sh all_multi_dtb
```

> Factory Android/Linux mirror defaults to multi-screen adaptive mirror, compatible with official 7-inch/10-inch/15-inch display, connected to any display can be directly lit



### SDK Add Compile Item

**Example: Added RK3568 K1 Buildroot compilation option**

* New configuration file

```
$ cd device/rockchip/rk3566_rk3568/
$ cp rockchip_rk3568_kickpi_k1_debian_defconfig rockchip_rk3568_kickpi_k1_buildroot_defconfig
```

* Modify the compilation configuration file

```
$ vim rockchip_rk3568_kickpi_k1_buildroot_defconfig

- RK_ROOTFS_SYSTEM_DEBIAN=y
+ RK_ROOTFS_SYSTEM_BUILDROOT=y
```



### toolchain

```
prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```



## 3. Android13.0 SDK compilation

### SDK Introduction

Kernel version: 5.10

Android system version: 13.0



### Get the SDK source code

* Network disk directory

```
1-SDK\rk356x-android13\
	rk356x-android13-*.tar.gz
```



* Copy the source archive to the build host directory

If the compilation host is a virtual machine, it cannot be compiled in a shared directory



* MD5 verifies file integrity

```
$ md5sum -c *.md5
```



* Unzip the source code compressed package

```
$ tar -zxvf rk356x-android13-*.tar.gz
$ cd rk356x-android13-*
$ git reset --hard
```



### Compile the full image

According to the CPU model, select the corresponding compilation command; if the compilation fails, you can try to solve it yourself, or contact technical customer service to solve it.

**Configure SDK compilation items **

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

>After the configuration is selected, the software will be configured to the selected device; if the device is switched, it needs to be reconfigured, otherwise it does not need to be configured multiple times.



**Compile full image**

```
$ ./build.sh -AUCKu
```

> Mirror generation directory：rockdev/Image-*/update-\*.img



Configure thread options, specify thread 32

```
$ ./build.sh -AUCKu -J32
```

> Note: If it fails when compiling Android, but there is no specific error, there is a high probability that the virtual machine configuration is too low;

Configure thread options, specify single thread

```
$ ./build.sh -AUCKu -J1
```

Or modify the default configuration in the build.sh and try again.

```diff
$ vim build.sh
-BUILD_JOBS=16
+BUILD_JOBS=1
$ ./build.sh -AUCKu
```



### Compile partition images separately

**Compile Uboot separately **

```
$ ./build.sh -U
```

> Image generation directory: rockdev/Image-\ */uboot.img



**Compile Kernel Separately**

```
$ ./build.sh -CK
```

> Image generation directory: rockdev/Image-\ */boot.img



**Compile Android separately**

```
$ ./build.sh -A
```

> Image generation directory: rockdev/Image-\ */super.img



**Android environment configuration**

Configure the command environment, you can use the command in the Android environment such as mm/get_build_var

```shell
// configuration environment
source build/envsetup.sh
```

Device configuration selection

```
$ lunch
According to the actual situation, select the corresponding equipment, such as'K1 'and'K1B' as rk3568.
61
```

![2a41026b5899c13dcc725f59858abd7](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/2a41026b5899c13dcc725f59858abd7.png)

Use related commands under Android, such as

```
$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
true
```



### Other commonly used commands

**Kernel Configuration**

```
$ ./build.sh -CKM
```

**Compile multi-screen adaptive mirroring**

```
$ ./build.sh -AUCKum
```

> Factory Android/Linux mirror defaults to multi-screen adaptive mirror, compatible with official 7-inch/10-inch/15-inch display, connected to any display can be directly lit
