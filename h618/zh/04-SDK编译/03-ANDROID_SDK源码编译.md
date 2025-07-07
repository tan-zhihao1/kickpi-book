# 2. Android SDK编译

## SDK介绍

Android SDK版本信息

内核版本：5.4

Android系统版本：12.0



## 获取SDK源码

* 网盘下载目录

```shell
1-SDK\Android12.0\
	h618-android12.0-xxxxxxxxxx.tar.gz
建议下载日期最新的版本
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

首次编译需要配置一下编译环境，后续不改变sdk位置，则无需重复配置

```shell
先进入longan目录
$ cd longan
然后执行命令
$ ./build.sh config
选择如下配置
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

然后再执行
$ cd -
```

选择主板

```
$ ./build.sh lunch
will lunch sdk

You're building on Linux
Lunch menu...pick a combo:

1. BoardConfig-kickpi-k2b-tablet.mk
2. BoardConfig-kickpi-k2b-tv.mk
3. BoardConfig-kickpi-k2c-tablet.mk
4. BoardConfig-kickpi-k2c-tv.mk

Which would you like? [0]:
```

> 说明
>
> BoardConfig-kickpi-'boadr'-'desktop'
>
> board：主板 k2b / k2c
>
> desktop：桌面 tv / tablet



## 编译完整镜像

```
$ ./build.sh
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img

注意：如果编译Android的时候失败了，没有明显的报错，可以修改build.sh 里面Android编译的进程数，将make -j32减小至你设备的实际情况。

## 单独编译分区镜像

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

**配置内核**

``` 
cd longan

./build.sh menuconfig
```

**单独编译Android**

```
$ ./build.sh android
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img

**设备树路径**

``` shell
h618-android12.0/longan/device/config/chips/h618/configs/p2/linux-5.4/
board-k2b.dts  board-k2c.dts
```



## 常见问题

### APP 、文件编译不生效问题

android 为惰性编译，直接替换后编译，一些APP、文件不一定编译到镜像中

可以通过如下其中一种方式解决

1. make installclean

2. rm out/target/product/apollo-p2/xxx/xxx

3. 编译的条件发生变化（比如文件名、APK名）
