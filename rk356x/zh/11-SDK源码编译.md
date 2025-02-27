# 11-SDK源码编译






## 1. 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

### 硬件要求

| 系统环境  | 要求        |
| --------- | ----------- |
| 系统版本  | Ubuntu22.04 |
| CPU核心数 | 4核以上     |
| 内存容量  | 16GB以上    |
| 硬盘容量  | 200GB以上   |



### 安装依赖软件包

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

> 软件包名称 会根据UBUNTU版本更新而变化
>
> 不同UBUNTU版本安装失败，可网络搜索对应的解决方法





## 2. Linux SDK编译

### SDK介绍

Rockchip Linux SDK由SDK源码包、文件系统镜像组成

SDK源码包内核版本5.10，支持Ubuntu20.04、Debian11文件系统



### 获取SDK源码

**网盘下载SDK源码压缩包**

网盘SDK目录如下

```
1-SDK源码\rk356x-linux\
	rk356x-linux-*.tar.gz
	rk356x-linux-*.md5
```



**拷贝源码压缩包到编译主机目录**

> 注意事项
>
> 1. 若编译主机为虚拟机，不可放在共享目录下编译



**MD5校验文件完整性**

```
$ md5sum -c *.md5
```



**解压源码压缩包**

```
$ tar -zxvf rk356x-linux-*.tar.gz

$ ls -al
	.git
```

> SDK源码压缩包通过Git仓库分享，可用ls -al命令查看到.git文件夹



**还原源码文件**

进入解压出的目录下，通过git还原源码

```
$ git reset --hard
```

> 注意：请在Linux下操作 并且安装了git
>



### 获取文件系统镜像

SDK源码包不包含文件系统镜像，需要参考下面步骤，单独解压拷贝文件系统到SDK源码

网盘提供debian、ubuntu文件系统镜像，可根据需求自行下载

> SDK提供buildroot的编译但是buildroot系统不做维护



**文件系统网盘目录**

```
1-SDK源码\rk356x-linux\
	linaro-rootfs-20230524.tar.gz
	ubuntu-rootfs-20230523.tar.gz
	buildroot-dl-20230607.tar.gz
```



**buildroot-dl-20230607.tar.gz使用方式 ：**

```
$ tar -zxvf buildroot-dl-*.tar.gz 
$ cp -rf dl buildroot/
```

> buildroot-dl-20230607.tar.gz :为dl库离线包用于节约第一次编译时间



**MD5校验文件完整性**

```
$ md5sum -c *.md5
```



**解压debian镜像文件**

```
$ tar -zxvf linaro-rootfs-*.tar.gz
$ cp linaro-rootfs.img debian/

$ ls debian/linaro-rootfs.img
```

> debian镜像文件的名称、目录必须保证一致
>
> 文件系统目录：debian/linaro-rootfs.img
>
> lunch 选择 debian，会使用此 linaro-rootfs.img 进行编译 



**解压ubuntu镜像文件**

```
$ tar -zxvf ubuntu-rootfs-*.tar.gz
$ cp ubuntu-rootfs.img ubuntu/

$ ls ubuntu/ubuntu-rootfs.img
```

> ubuntu镜像文件的名称、目录必须保证一致
>
> 文件系统目录：ubuntu/ubuntu-rootfs.img
>
> lunch 选择 ubuntu，会使用此 ubuntu-rootfs.img 进行编译



### 编译完整镜像

首次执行build.sh进行SDK编译，必须要选择编译配置信息，如：CPU型号、板卡型号、文件系统型号

具体板卡型号差异可查看 `入门文档介绍`



**SDK编译配置**

```
$ ./build.sh lunch
	
    You're building on Linux
    Lunch menu...pick a combo:
    1. rk356x
    2. rk3588
    Which would you like? [0]:	# 选择CPU型号

    You're building on Linux
    Lunch menu...pick a combo:
	1. BoardConfig-rk3566-kickpi-tx66.mk
	2. BoardConfig-rk3568-kickpi-tx68.mk
    Which would you like? [0]:	# 选择板卡型号

    You're building on Linux
    Lunch menu...pick a combo:
    0. default RootfsConfig.mk
    1. RootfsConfig-buildroot.mk
    2. RootfsConfig-debian.mk
    3. RootfsConfig-ubuntu.mk
    Which would you like? [0]:	# 选择文件系统型号
```

> 配置命令首次编译执行一次即可



**编译镜像**

```
$ ./build.sh
```

> 镜像生成目录：rockdev/update-\*.img
>
> 若编译失败，可尝试自行解决，亦可联系技术支持客服解决



### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh uboot
```

> 镜像生成目录：rockdev/uboot.img

**单独编译Kernel**

```
$ ./build.sh kernel
```

> 镜像生成目录：rockdev/boot.img

**单独编译Buildroot**

```
$ ./build.sh buildroot
```

> 镜像生成目录：rockdev/rootfs.img



### 其他常用命令

**内核配置**

```
$ ./build.sh kernel-config
```

**buildroot配置**

```
$ ./build.sh menuconfig
```

**编译多屏自适应镜像**

```
$ ./build.sh all_multi_dtb
```

> 出厂Android/Linux镜像默认为多屏自适应镜像，兼容官方7寸/10寸/15寸显示屏，连接任一显示屏可直接点亮



### SDK新增编译项

**示例：新增RK3568 K1 Buildroot编译选项**

* 新增配置文件

```
$ cd device/rockchip/rk3566_rk3568/
$ cp rockchip_rk3568_kickpi_k1_debian_defconfig rockchip_rk3568_kickpi_k1_buildroot_defconfig
```

* 修改编译配置文件

```
$ vim rockchip_rk3568_kickpi_k1_buildroot_defconfig

- RK_ROOTFS_SYSTEM_DEBIAN=y
+ RK_ROOTFS_SYSTEM_BUILDROOT=y
```



### 工具链

```
prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```



## 3. Android13.0 SDK编译

### SDK介绍

内核版本：5.10

Android系统版本：13.0



### 获取SDK源码

* 网盘目录

```
1-SDK源码\rk356x-android13\
	rk356x-android13-*.tar.gz
```



* 拷贝源码压缩包到编译主机目录

  若编译主机为虚拟机，不可放在共享目录下编译



* MD5校验文件完整性

```
$ md5sum -c *.md5
```



* 解压源码压缩包

```
$ tar -zxvf rk356x-android13-*.tar.gz
$ cd rk356x-android13-*
$ git reset --hard
```



### 编译完整镜像

根据CPU型号，选择对应编译命令；若编译失败，可尝试自行解决，或联系技术客服解决

**配置SDK编译项**

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

>配置选择后，软件会配置成选择的设备；若切换设备需要重新配置，否则无需多次配置



**编译完整镜像**

```
$ ./build.sh -AUCKu
```

> 镜像生成目录：rockdev/Image-*/update-\*.img



配置线程选项，指定线程32

```
$ ./build.sh -AUCKu -J32
```

> 注意：如果遇到编译Android时就失败了，但是没什么具体报错，大概率虚拟机配置过低；

配置线程选项，指定单线程

```
$ ./build.sh -AUCKu -J1
```

或修改 build.sh 里面的默认配置后重新尝试

```diff
$ vim build.sh
-BUILD_JOBS=16
+BUILD_JOBS=1
$ ./build.sh -AUCKu
```



### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh -U
```

> 镜像生成目录：rockdev/Image-\*/uboot.img



**单独编译Kernel**

```
$ ./build.sh -CK
```

> 镜像生成目录：rockdev/Image-\*/boot.img



**单独编译Android**

```
$ ./build.sh -A
```

> 镜像生成目录：rockdev/Image-\*/super.img



**android 环境配置**

配置命令环境，可以使用 mm / get_build_var 等安卓环境下命令

```shell
// 配置环境
source build/envsetup.sh
```

设备配置选择

```
$ lunch
根据实际，选择对应的设备，比如'K1'和'K1B'为 rk3568
61
```

![2a41026b5899c13dcc725f59858abd7](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/2a41026b5899c13dcc725f59858abd7.png)

Android下相关命令使用，比如

```
$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
true
```



### 其他常用命令

**内核配置**

```
$ ./build.sh -CKM
```

**编译多屏自适应镜像**

```
$ ./build.sh -AUCKum
```

> 出厂Android/Linux镜像默认为多屏自适应镜像，兼容官方7寸/10寸/15寸显示屏，连接任一显示屏可直接点亮
