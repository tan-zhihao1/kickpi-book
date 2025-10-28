# 02-LINUX_SDK编译

## 符号说明

< SDK > : 指代SDK源码路径



## SDK介绍

Rockchip Linux SDK由SDK源码包、文件系统镜像组成

SDK源码包内核版本5.10，支持Ubuntu20.04、Debian11文件系统

> SDK里有提供buildroot系统但**buildroot系统不做维护**



## 获取SDK源码

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

$ ls -al <SDK> 
	.git
```

> SDK源码压缩包通过Git仓库分享，可用ls -al命令查看到.git文件夹



**还原源码文件**

进入解压出的目录下，通过git还原源码

```
$ cd <SDK> 
$ git reset --hard
```

> 注意：请在Linux下操作 并且安装了git



## 获取文件系统镜像

SDK源码包不包含文件系统镜像，需要参考下面步骤，单独解压拷贝文件系统到SDK源码

网盘提供debian、ubuntu文件系统镜像，可根据需求自行下载



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
$ cp -rf ./dl <SDK>/buildroot/

$ ls <SDK>/buildroot/dl
```

> buildroot-dl-*.tar.gz :为dl库离线包用于节约第一次编译时间



**MD5校验文件完整性**

```
$ md5sum -c *.md5
```



**解压debian镜像文件**

```
$ tar -zxvf linaro-rootfs-*.tar.gz
$ cp linaro-rootfs.img <SDK>/debian/

$ ls <SDK>/debian/linaro-rootfs.img
```

> debian镜像文件的名称、目录必须保证一致
>
> 文件系统目录：debian/linaro-rootfs.img
>
> lunch 选择 debian，会使用此 linaro-rootfs.img 进行编译 



**解压ubuntu镜像文件**

```
$ tar -zxvf ubuntu-rootfs-*.tar.gz
$ mkdir -p <SDK>/ubuntu/
$ cp ubuntu-rootfs.img <SDK>/ubuntu/

$ ls <SDK>/ubuntu/ubuntu-rootfs.img
```

> ubuntu镜像文件的名称、目录必须保证一致
>
> 文件系统目录：ubuntu/ubuntu-rootfs.img
>
> lunch 选择 ubuntu，会使用此 ubuntu-rootfs.img 进行编译



## 编译完整镜像

首次执行build.sh进行SDK编译，必须要选择编译配置信息，如：CPU型号、板卡型号、文件系统型号

具体板卡型号差异可查看 `入门文档介绍`



**SDK编译配置**

```
$ ./build.sh lunch

Pick a chip:

1. rk3566_rk3568
2. rk3588
Which would you like? [1]: 1
Switching to chip: rk3566_rk3568
Pick a defconfig:

1. rockchip_defconfig
2. rockchip_rk3562_kickpi_k3_buildroot_defconfig
3. rockchip_rk3562_kickpi_k3_debian_defconfig
4. rockchip_rk3562_kickpi_k3_ubuntu_defconfig
5. rockchip_rk3568_kickpi_k1_buildroot_defconfig
6. rockchip_rk3568_kickpi_k1_debian_defconfig
7. rockchip_rk3568_kickpi_k1_ubuntu_defconfig
8. rockchip_rk3568_kickpi_k1b_buildroot_defconfig
9. rockchip_rk3568_kickpi_k1b_debian_defconfig
10. rockchip_rk3568_kickpi_k1b_ubuntu_defconfig
Which would you like? [1]: 
```

> 配置命令首次编译执行一次即可



**编译镜像**

```
$ ./build.sh
```

> 镜像生成目录：rockdev/update-\*.img
>
> 若编译失败，可尝试自行解决，亦可联系技术支持客服解决
>
> 如果有接屏，请先选择你使用的屏配置：[屏设备树配置](..\05-外设驱动\LCD.md#LCDDriver) 



## 单独编译分区镜像

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



## 其他常用命令

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



## SDK新增编译项

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



## 工具链

```
prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```



## 常见问题

### buildroot 编译失败问题

由于下载buildroot相关源码会下载失败，可以修改对应镜像源。

例如：rk3568 修改添加镜像源

```
--- a/buildroot/configs/rockchip_rk3568_defconfig
+++ b/buildroot/configs/rockchip_rk3568_defconfig
+BR2_KERNEL_MIRROR="https://mirrors.ustc.edu.cn/kernel"
+BR2_GNU_MIRROR="https://mirrors.ustc.edu.cn/gnu"
+BR2_CPAN_MIRROR="https://mirrors.ustc.edu.cn/CPAN"

--- a/buildroot/configs/rockchip_rk3568_recovery_defconfig
+++ b/buildroot/configs/rockchip_rk3568_recovery_defconfig
+BR2_KERNEL_MIRROR="https://mirrors.ustc.edu.cn/kernel"
+BR2_GNU_MIRROR="https://mirrors.ustc.edu.cn/gnu"
+BR2_CPAN_MIRROR="https://mirrors.ustc.edu.cn/CPAN"
```

> k1 / k1b 对应 rk3568
>
> k3 对应 rk3562
>
> k8 对应 rk3588



### 编译出的镜像烧录后屏幕不亮

我们网盘提供的是多屏自适应镜像，需要特殊指令编译，参考[编译多屏自适应镜像](#multi)

如果你只是完整编译，需要自己去选择你使用的屏，参考文档

[kickpi-book/common/en/driver/LCD](../../../common/en/driver/LCD.md)

[kickpi-book/common/zh/driver/LCD](../../../common/zh/driver/LCD.md)



### 编译报错问题日志抓取

```
$ ./build.sh | tee build.log
```

> 通过 | tee build.log 组合将编译信息抓取到 build.log 中。
