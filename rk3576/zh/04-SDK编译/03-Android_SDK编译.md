# 03-Android_SDK编译

### SDK介绍

内核版本：6.1

Android系统版本：14.0



### 获取SDK源码

* 网盘目录

```
1-SDK软件源码
├── Android14
    ├── rk3576-android14.0-*.md5sum
    └── rk3576-android14.0-*.tar.gz
```

>  *这里一般指代日期，下面同，实际名称以网盘为准

* 拷贝源码压缩包到编译主机目录

  若编译主机为虚拟机，不可放在共享目录下编译

* MD5校验文件完整性

```
md5sum rk3576-android14.0-*.tar.gz
```

> 判断结果和 rk3576-android14.0-*.md5sum 内容一致，则文件下载完整

* 解压源码压缩包

```
$ mkdir rk3576-android
$ tar -zxvf rk3576-android14.0-*.tar.gz -C rk3576-android/
$ cd rk3576-android/
$ git reset --hard
```



### 配置环境

> 前提：确保编译硬件要求，以及[编译环境搭建](01-编译环境搭建.md)成功

单独编译或全部编译前先配置环境

```bash
$ ./build.sh lunch
will lunch sdk

You're building on Linux
Lunch menu...pick a combo:

1. rk3576
Which would you like? [0]: 1

You're building on Linux
Lunch menu...pick a combo:

1. BoardConfig.mk
2. BoardConfig-rk3576-kickpi-k7.mk
Which would you like? [0]: 2
switching to board: /home/huangcm/A/sdk/rk3576-android14.0/device/rockchip/rk3576/BoardConfig-rk3576-kickpi-k7.mk
```



### 全部编译

> 提示：确保编译硬件要求，以及[编译环境搭建](01-编译环境搭建#BuildEnv-K7)成功

标准镜像

```
./build.sh -AUCKu
```

>镜像 rockdev/Image-rk3576_u/update-rk3576-kickpi-k7-android-*.img

多屏识别镜像

```
$ ./build.sh -UCKAum
```



### 单独编译

* 以下命令需先进行[配置环境](#androidEnv-K7)

单编Uboot

```
./build.sh -Uu
```

单编安卓

```
./build.sh -Au
```

单编kernel

```
./build.sh -CKu
```

配置 Kernel defconfig

```
./build.sh -M
```



### 其他说明

**固件说明**

完整编译后会生成如下文件：

```
(源码)/rockdev/Image-rk3576_u/
rockdev/Image-rk3576_u/
├── baseparameter.img
├── boot-debug.img
├── boot.img
├── config.cfg
├── dtbo.img
├── MiniLoaderAll.bin
├── misc.img
├── parameter.txt
├── pcba_small_misc.img
├── pcba_whole_misc.img
├── recovery.img
├── resource.img
├── super.img
├── uboot.img
├── update.img
├── update-rk3576-kickpi-k7-android-*.img
└── vbmeta.img
```

烧写的镜像为 `(源码)/rockdev/Image-rk3576_u/update-rk3576-kickpi-k7-android-*.img`

烧录详见 - `10-系统镜像烧录`



**多屏识别编译**

```
$ ./build.sh -UCKAum
```



**userdebug 和 user 编译**

默认为userdebug模式编译，如果需要user版本镜像则需要修改对应编译mk

```diff
vim device/rockchip/rk3576/BoardConfig-rk3576-kickpi-k7.mk
-export BUILD_VARIANT=userdebug
+export BUILD_VARIANT=user
```



**android 环境配置**

配置环境，可以使用 mm / get_build_var 等安卓环境下命令

```shell
// 配置环境
source build/envsetup.sh
lunch rk3576_u-userdebug

// 比如
$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
true
```

dts 路径

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android.dts
```

defconfig 路径

```
kernel-6.1/arch/arm64/configs/rockchip_defconfig
```



### 常见问题

**由于代码位置变化，路径错误导致 android 编译错误**

![image-20241125155203743](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241125155203743.png)

需要清除数据

```shell
source build/envsetup.sh
lunch rk3576_u-userdebug
make clean -j32
```

重新全部编译



**编译空间不足**

目前默认线程 -j32，通过降低线程数进行编译

方式一： 

修改编译方式，带 -J 线程

```shell
如： 修改线程为16，-J16
./build.sh -AUCKu -J16
```

方式二：

修改默认线程

```diff
$ vim device/rockchip/common/build/rockchip/build.sh
-BUILD_JOBS=32
+BUILD_JOBS=16
```

