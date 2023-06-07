# 02初级篇-sdk编译

测试

### 1. 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

#### 硬件要求

| 系统环境   | 要求          |
| ------ | ----------- |
| 系统版本   | Ubuntu20.04 |
| CPU核心数 | 4核以上        |
| 内存容量   | 8GB以上       |
| 硬盘容量   | 200GB以上     |

#### 安装依赖软件包

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev g++-multilib gcc-multilib gnupg gperf libncurses5-dev imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev libxml2 libxml2-utils schedtool lzop pngcrush rsync yasm zip zlib1g-dev python device-tree-compiler python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect

$ sudo pip install pyelftools
```

> 软件包名称 会根据UBUNTU版本更新而变化。不同UBUNTU版本安装失败，可网络搜索对应的解决方法

### 2. Linux SDK编译

#### 获取SDK源码

* SDK网盘目录

```
1-SDK源码\rk356x-linux\
	rk356x-linux-*.tar.gz
```

* 解压源码压缩包

```
$ tar -zxvf rk356x-linux-*.tar.gz
$ git reset --hard
```

> SDK源码仅包含git仓库，需要按照以上命令恢复出源码

#### 获取Rootfs镜像

* 文件系统网盘目录

```
1-SDK源码\rk356x-linux\
	linaro-rootfs-20230524.tar.gz
	ubuntu-rootfs-20230523.tar.gz
```

* 解压debian镜像文件

```
$ tar -zxvf linaro-rootfs-*.tar.gz
$ cp linaro-rootfs.img debian/

$ ls debian/linaro-rootfs.img
```

> debian镜像文件的名称、目录必须保证一致
>
> 文件系统目录：debian/linaro-rootfs.img

* 解压ubuntu镜像文件

```
$ tar -zxvf ubuntu-rootfs-*.tar.gz
$ cp ubuntu-rootfs.img.img ubuntu/

$ ls ubuntu/ubuntu-rootfs.img
```

> ubuntu镜像文件的名称、目录必须保证一致
>
> 文件系统目录：ubuntu/ubuntu-rootfs.img

#### 编译完整镜像

首次执行build.sh进行SDK编译，必须要选择编译配置信息，如：CPU型号、板卡型号、文件系统型号

具体板卡型号差异可查看 `入门文档介绍`

* SDK编译配置

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

> 首次编译执行一次即可

* 编译镜像

```
$ ./build.sh
```

> 镜像生成目录：rockdev/update-\*.img
>
> 若编译失败，可尝试自行解决，亦可联系技术支持客服解决

#### 单独编译分区镜像

**Uboot**

```
$ ./build.sh uboot
```

> 镜像生成目录：rockdev/uboot.img

**Kernel**

```
$ ./build.sh kernel
```

> 镜像生成目录：rockdev/boot.img

**Buildroot**

```
$ ./build.sh buildroot
```

> 镜像生成目录：rockdev/rootfs.img

### 3. Android SDK编译

#### 获取SDK源码

* 网盘目录

```
1-SDK源码\rk356x-android13\
	rk356x-android13-*.tar.gz
```

* 解压源码压缩包

```
$ tar -zxvf rk356x-android13-*.tar.gz
$ git reset --hard
```

#### 编译完整镜像

根据CPU型号，选择对应编译命令

若编译失败，可尝试自行解决，或联系技术客服解决

* RK3566平台

```
$ source build/envsetup.sh
$ lunch rk3566_t-userdebug
$ ./build.sh -AUCKu 
```

> 镜像生成目录：rockdev/Image-rk3566\_t/update-\*.img

* RK3568平台

```
$ source build/envsetup.sh
$ lunch rk3568_t-userdebug
$ ./build.sh -AUCKu
```

> 镜像生成目录：rockdev/Image-rk3568\_t/update-\*.img

#### 单独编译分区镜像

**Uboot**

```
$ source build/envsetup.sh
$ lunch
$ ./build.sh -U
```

> 镜像生成目录：rockdev/Image-\*/uboot.img

**Kernel**

```
$ source build/envsetup.sh
$ lunch
$ ./build.sh -CK
```

> 镜像生成目录：rockdev/Image-\*/boot.img

**Android**

```
$ source build/envsetup.sh
$ lunch
$ ./build.sh -A
```

> 镜像生成目录：rockdev/Image-\*/super.img
