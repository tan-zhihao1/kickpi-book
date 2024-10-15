# 11-SDK源码编译






## 1. 编译环境搭建

此编译环境配置 适用 Linux SDK

### 硬件要求

| 系统环境  | 要求        |
| --------- | ----------- |
| 系统版本  | Ubuntu18.04 |
| CPU核心数 | 4核以上     |
| 内存容量  | 16GB以上    |
| 硬盘容量  | 200GB以上   |

> ubuntu22.04 经过测试，编译会有大量报错，不建议使用



### 安装依赖软件包

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
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

Tina5.0 Linux SDK支持构建三个文件系统，分别为buildroot、openwrt、tina



### 获取SDK源码

**网盘下载SDK源码压缩包**

网盘SDK目录如下

```
1-SDK源码\t113-linux\
	t113-linux-*.tar.gz
	t113-linux-*.md5
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
$ tar -zxvf t113-linux-*.tar.gz
```

> SDK源码压缩包仅包含Git仓库，可用ls -al命令查看到.git文件夹



**还原源码文件**

```
$ git reset --hard
```



### 获取Rootfs镜像

* 文件系统网盘目录

```
1-SDK源码\t113-linux\
	linaro-rootfs-20230524.tar.gz
	ubuntu-rootfs-20230523.tar.gz
```



* MD5校验文件完整性

```
$ md5sum -c *.md5
```



* 解压ubuntu镜像文件（暂不支持）







### 编译完整镜像

首次执行build.sh进行SDK编译，必须要选择编译配置信息，如：CPU型号、板卡型号、文件系统型号

具体板卡型号差异可查看 `入门文档介绍`



* SDK编译配置

```
$ ./build.sh lunch

======you are building t113 linux======
1. BoardConfig-t113-s3-kickpi-k4b-buildroot.mk
which board would you like: 
```

> 配置命令首次编译执行一次即可



* 编译镜像

```
$ ./build.sh
```

> 镜像生成目录：out/t113_linux_evb1_auto_uart0.img
>
> 若编译失败，可尝试自行解决，亦可联系技术支持客服解决



### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh uboot
```



**单独编译Kernel**

```
$ ./build.sh kernel
```



**单独编译Buildroot**

```
$ ./build.sh buildroot_rootfs
```



### 其他常用命令

**内核配置**

```
$ ./build.sh kernel-config
```



**buildroot配置**

```
$ ./build.sh buildroot_menuconfig
$ ./build.sh buildroot_saveconfig
```



