# 02-Linux_SDK编译

### SDK介绍

内核版本：6.1

Debian系统版本：12

Ubuntu系统版本：2404



### 获取SDK源码

* 网盘目录

```
├── 1-SDK
│   └── Linux
│       ├── rootfs
│       │   ├── linaro-rootfs-*.img
│       │   └── ubuntu-rootfs-*.img
│       └── sdk
│           ├── rk3576-linux-*.md5sum
│           └── rk3576-linux-*.tar.gz
```

>  *这里一般指代日期，下面同，实际名称以网盘为准

1. 拷贝源码压缩包到编译主机目录（若编译主机为虚拟机，不可放在共享目录下编译）

2. MD5校验文件完整性

```
md5sum rk3576-linux-*.tar.gz
```

> 判断结果和 rk3576-linux-*.md5sum 内容一致，则文件下载完整

3. 解压源码压缩包

```
$ mkdir rk3576-linux
$ tar -zxvf md5sum rk3576-linux-*.tar.gz -C rk3576-linux/
$ cd rk3576-linux/
$ git reset --hard
```



### 配置环境

> 前提：确保编译硬件要求，以及[编译环境搭建](01-编译环境搭建.md)成功

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

标准镜像

```
$ ./build.sh 
```

多屏识别镜像

```
$ ./build.sh all_multi_dtb
```



### 单独编译

**Kernel defconfig 配置 **

```
$ ./build.sh kernel-config
```



**uboot 编译**

```
$ ./build.sh uboot
```



**kernel 编译**

方式一

```
$ ./build.sh kernel
```

方式二

```
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3576.config
$ make ARCH=arm64 rk3576-kickpi-k7-linux.img -j24
```



**kernel 多屏识别编译**

```
$ ./build.sh kernel_multi_dtb
```



**Debian 编译**

配置环境

```
$ sudo dpkg -i debian/ubuntu-build-service/packages/*
$ sudo apt-get install -f
```

编译 linaro-rootfs.img

```
$ ./build.sh debian
```

> 此命令若 debian/linaro-rootfs.img 不存在，则 debian/linaro-rootfs.img 会通过编译生成



**Ubuntu 编译**

配置环境

```
$ sudo apt-get install binfmt-support qemu-user-static --reinstall
```

编译 linaro-rootfs.img

```
$ ./build.sh ubuntu
```

> 此命令若 ubuntu/ubuntu-rootfs.img 不存在，则 ubuntu-rootfs.img 会通过编译生成



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

烧写的镜像为 **SDK/rockdev/update-rk3576-kickpi-k7-linux-\*-*.img**



**dts 路径**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-linux.dts
```



**defconfig 路径**

```
kernel-6.1/arch/arm64/configs/rockchip_linux_defconfig
```



**u-boot defconfig 路径**

```
u-boot/configs/rk3576_defconfig
```



**u-boot defconfig 配置**

```
$ cd u-boot/
$ make rk3576_defconfig 
$ make menuconfig
$ make savedefconfig
$ cp defconfig configs/rk3576_defconfig
```



## 常见问题

* 打开kernel的内核配置后，WiFi失效了

因为RK的WiFi驱动是用的自动适配的方法 
WiFi驱动位于：external/rkwifibt/drivers/
最后都以KO的形式存在于文件系统中`/lib/modules`下

需要重新编译拷贝最新的WiFi驱动到文件系统中

```
$ cd <文件系统路径>/
$ ./mk-image.sh
```

> 文件系统路径： ubuntu 或  debian
