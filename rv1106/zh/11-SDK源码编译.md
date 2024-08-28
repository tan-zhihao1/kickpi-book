# 11-SDK源码编译

## 1. 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

### 硬件要求

| 系统环境  | 要求        |
| --------- | ----------- |
| 系统版本  | 推荐Ubuntu18.04 |
| CPU核心数 | 4核以上     |
| 内存容量  | 16GB以上    |
| 硬盘容量  | 20GB以上   |



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





## 2. Linux SDK编译

### SDK介绍

Rockchip Linux SDK由SDK源码包、文件系统镜像组成


### 获取SDK源码

**网盘下载SDK源码压缩包**

网盘SDK目录如下

```
TODO
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
TODO
```

> SDK源码压缩包仅包含Git仓库，可用ls -al命令查看到.git文件夹



**还原源码文件**

```
$ git reset --hard
```

### 获取Rootfs镜像

* 文件系统网盘目录

```
TODO
```


* MD5校验文件完整性

```
$ md5sum -c *.md5
```


* 解压debian镜像文件

```
TODO
```


* 解压ubuntu镜像文件

```
TODO
```




### 编译完整镜像

首次执行进行SDK编译，必须要选择编译配置信息

具体板卡型号差异可查看 `入门文档介绍`

* SDK编译选择板级配置

```
$ ./build.sh lunch

...
----------------------------------------------------------------
4. BoardConfig_IPC/BoardConfig-EMMC-NONE-RV1106_KICKPI_K6B-IPC.mk
                             boot medium(启动介质): EMMC
                          power solution(电源方案): NONE
                        hardware version(硬件版本): RV1106_KICKPI_K6B
                             application(应用场景): IPC
----------------------------------------------------------------
...

Which would you like? [0]: 4  #选择对应板级配置的序号

```

> 配置命令首次编译执行一次即可


* 编译镜像

```
$ ./build.sh
```

> 镜像生成目录：output/image/
>
> 若编译失败，可尝试自行解决，亦可联系技术支持客服解决


### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh uboot
```

> 镜像生成目录：output/image/download.bin、output/image/idblock.img 和 output/image/uboot.img



**单独编译Kernel**

```
$ ./build.sh kernel
```

> 镜像生成目录：output/image/boot.img



**单独编译rootfs**

```
$ ./build.sh rootfs
```

> 编译后使用./build.sh firmware命令打包成rootfs.img
> 镜像生成目录：output/image/rootfs.img


**单独编译media**

```
$ ./build.sh media
```
> ⽣成⽂件的存放⽬录：output/out/media_out


**编译参考应用**

```
./build.sh app
```
> ⽣成⽂件的存放⽬录：output/out/app_out
> 注：app依赖media

**编译内核驱动**
```
./build.sh driver
```
> ⽣成⽂件的存放⽬录：output/out/sysdrv_out/kernel_drv_ko/


### 其他常用命令

**内核配置**

```
$ ./build.sh kernel-config
```



**buildroot配置**

```
$ ./build.sh menuconfig
```



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



**编译完整镜像**

```
$ ./build.sh -AUCKu
```

> 镜像生成目录：rockdev/Image-*/update-\*.img



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



### 其他常用命令

**内核配置**

```
$ ./build.sh -CKM
```

