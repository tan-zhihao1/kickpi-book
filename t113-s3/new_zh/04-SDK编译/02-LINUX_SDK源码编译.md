# 02-LINUX_SDK源码编译

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
