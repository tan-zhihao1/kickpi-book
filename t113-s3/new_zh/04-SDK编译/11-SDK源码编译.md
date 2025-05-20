# 11-SDK源码编译





> 

## 2. Linux SDK编译

### SDK介绍

Tina5.0 Linux SDK支持构建两个文件系统，分别为buildroot、ubuntu



### 获取SDK源码

**网盘下载SDK源码压缩包**

网盘SDK目录如下

```
1-SDK源码\linux\sdk\xxxxxxx\
	t113-linux-*.tar.gz
	t113-linux-*.md5
```



**拷贝源码压缩包到编译主机目录**

> 注意事项
>
> 1. 若编译主机为虚拟机，不可放在共享目录下编译



**MD5校验文件完整性**

```shell
$ md5sum -c *.md5
```



**解压源码压缩包**

```shell
$ tar -zxvf t113-linux-*.tar.gz
```

> 不要使用root权限！！！
> SDK源码压缩包仅包含Git仓库，可用ls -al命令查看到.git文件夹



**还原源码文件**

```shell
$ cd t113-linux
$ git reset --hard
```
> 不要使用root权限！！！

### 获取Rootfs镜像

* 文件系统网盘目录

```
1-SDK源码\linux\rootfs\
	buildroot-dl-*.tar.gz
	ubuntu-rootfs-*.tar.gz
```

* buildroot文件系统

提供的buildroot-dl-*.tar.gz压缩包，可用于离线编译Buildroot文件系统,，减少编译时下载源码的时间

将buildroot-dl-*.tar.gz解压至buildroot/buildroot-201902目录下

* ubuntu文件系统

ubuntu-rootfs-*.tar.gz是ubuntu文件系统压缩包，搭配SDK源码编译可生成ubuntu系统镜像

将ubuntu-rootfs-*.tar.gz拷贝至SDK的device/config/rootfs_tar目录下并重命名为ubuntu-armhf.tar.gz




### 编译完整镜像

首次执行build.sh进行SDK编译，必须要选择编译配置信息，如：CPU型号、板卡型号、文件系统型号

* SDK编译配置

```shell
$ ./build.sh lunch

======you are building t113 linux======
1. BoardConfig-t113-s3-kickpi-k4b-buildroot.mk
2. BoardConfig-t113-s3-kickpi-k4b-ubuntu.mk
which board would you like (1-2): 
```

> 配置命令首次编译执行一次即可



* 编译镜像

```shell
./build.sh       \\编译
./build.sh pack  \\打包生成镜像
```

> 镜像生成目录：out/t113_linux_evb1_auto_uart0.img
>
> 若编译失败，可尝试自行解决，亦可联系技术支持客服解决



### 单独编译分区镜像

**单独编译Uboot**

```shell
$ ./build.sh uboot
```



**单独编译Kernel**

```shell
$ ./build.sh kernel
```



**单独编译Buildroot**

```shell
$ ./build.sh buildroot_rootfs
```



### 其他常用命令

**内核配置**

```shell
$ ./build.sh menuconfig
$ ./build.sh saveconfig
```

使用的defconfig文件为

device/config/chips/t113/configs/evb1_auto/linux-5.4/config-5.4

**buildroot配置**

```shell
$ ./build.sh buildroot_menuconfig
$ ./build.sh buildroot_saveconfig
```

使用的defconfig文件为

buildroot/buildroot-201902/configs/sun8iw20p1_t113_defconfig

## 3. 编译工具链

编译完成时工具链位于

``` shell
out/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_arm-linux-gnueabihf/bin
```



