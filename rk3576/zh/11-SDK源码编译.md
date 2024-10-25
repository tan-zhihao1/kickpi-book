# SDK源码编译

[TOC]

## 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

### 硬件要求

| 系统环境  | 要求            |
| --------- | --------------- |
| 系统版本  | 推荐Ubuntu18.04 |
| CPU核心数 | 4核以上         |
| 内存容量  | 16GB以上        |
| 硬盘容量  | 20GB以上        |

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



## Android 编译

配置环境

```
source build/envsetup.sh
lunch rk3576_u-userdebug
```

全部编译

```
./build.sh -AUCKu
```



详情可参考

```
（源码）/RKDocs/android/RK3576_Developer_Guide_Android14_SDK_CN.pdf
```



## 文档参考

源码下有 RK 官方的指导文件

```
（源码）/RKDocs/
```



