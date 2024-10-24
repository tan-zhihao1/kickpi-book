# 11-SDK源码编译

## 编译环境搭建

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



## Linux SDK编译

### SDK介绍

Rockchip Linux SDK由SDK源码包、文件系统镜像组成


### 获取SDK源码

**网盘下载SDK源码压缩包**

网盘SDK目录如下

```

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

```

> SDK源码压缩包仅包含Git仓库，可用ls -al命令查看到.git文件夹



**还原源码文件**

```
$ git reset --hard
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
>
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
>
> 注：app依赖media



**编译内核驱动**

```
./build.sh driver
```
> ⽣成⽂件的存放⽬录：output/out/sysdrv_out/kernel_drv_ko/



### SDK新增编译项

**示例：新增编译选项**

```
+ project/cfg/BoardConfig_IPC/BoardConfig-EMMC-NONE-RV1106_KICKPI_K6B-IPC.mk
```



## buildroot

代码路径：

```
源码：
sysdrv/tools/board/buildroot/buildroot-2024.02.6.tar.gz
生成路径：
sysdrv/source/buildroot/buildroot-2024.02.6/
```

配置：

```
make kickpi_k6b_defconfig -C sysdrv/source/buildroot/buildroot-2024.02.6/
make menuconfig -C sysdrv/source/buildroot/buildroot-2024.02.6/
make savedefconfig -C sysdrv/source/buildroot/buildroot-2024.02.6/
diff sysdrv/source/buildroot/buildroot-2024.02.6/configs/kickpi_k6b_defconfig sysdrv/tools/board/buildroot/kickpi_k6b_defconfig
cp -v sysdrv/source/buildroot/buildroot-2024.02.6/configs/kickpi_k6b_defconfig sysdrv/tools/board/buildroot/kickpi_k6b_defconfig
```



## dts

```
sysdrv/source/kernel/arch/arm/boot/dts/rv1106g-kickpi-k6b.dts
```



## defconfig

```
sysdrv/source/kernel/arch/arm/configs/rv1106_defconfig
```



```
cd sysdrv/source/objs_kernel
make ARCH=arm rv1106_defconfig
make ARCH=arm menuconfig
make ARCH=arm savedefconfig
diff defconfig ../kernel/arch/arm/configs/rv1106_defconfig
cp defconfig ../kernel/arch/arm/configs/rv1106_defconfig
```

