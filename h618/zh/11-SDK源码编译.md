# 11-SDK源码编译






## 1. 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

### 硬件要求

| 系统环境  | 要求        |
| --------- | ----------- |
| 系统版本  | Ubuntu20.04 |
| CPU核心数 | 4核以上     |
| 内存容量  | 16GB以上    |
| 硬盘容量  | 200GB以上   |



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



## 2. Android SDK编译

### SDK介绍

Android SDK版本信息

内核版本：5.4

Android系统版本：12.0



### 获取SDK源码

* 网盘下载目录

```
1-SDK软件源码\Android12.0\
	h618-android12.0-20240320.tar.gz
```



* 拷贝源码压缩包到编译主机目录

  > 注意事项：
  >
  > 1. 源码不可放在共享目录下编译
  >
  > 2. 编译主机不可用ROOT账号登陆



* MD5校验文件完整性

```
$ md5sum -c *.md5
```



* 合并源码压缩包

```
$ cat *tar.gz_0* > sdk.tar.gz
```

> 原始压缩包默认可能超过100G以上，所以采用分包压缩的方式
>
> 在解压前，需要先把压缩包合并起来



* 解压源码压缩包

```
$ tar -zxvf *.tar.gz
```

> 解压完成后，查看源码目录仅有.git文件夹，需要执行下一步才能完成恢复出源码



* Git恢复源码目录

```
$ git reset --hard
```



## 首次编译环境配置

首次配置一下环境

```
cd longan
./build.sh config
```



编译选项

```
Welcome to mkscript setup progress
All available platform:
   0. android
   1. linux
Choice [android]: 0
All available ic:
   0. h618
Choice [h618]: 0
All available board:
   0. ft
   1. p1
   2. p2
   3. p7
   4. p7l
   5. perf1
   6. perf2
   7. perf3
   8. qa
Choice [p2]: 2
All available flash:
   0. default
   1. nor
Choice [default]: 0
All available kern_ver:
   0. linux-5.4
Choice [linux-5.4]: 0
All available arch:
   0. arm
   1. arm64
Choice [arm64]: 1
```





### 编译完整镜像

```
$ ./build.sh
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh uboot
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



**单独编译Kernel**

```
$ ./build.sh kernel
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



**单独编译Android**

```
$ ./build.sh android
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



## 3. ARMBIAN SDK

建议参考ARMBIAN官方文档
