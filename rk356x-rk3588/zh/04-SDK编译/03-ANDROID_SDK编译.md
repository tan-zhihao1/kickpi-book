# 03-ANDROID_SDK编译

### SDK介绍

内核版本：5.10

Android系统版本：13.0



### 获取SDK源码

* 网盘目录

```
1-SDK源码\rk356x-android13\
	rk356x-android13-*.tar.gz
```



* 拷贝源码压缩包到编译主机目录

  若编译主机为虚拟机，不可放在共享目录下编译



* MD5校验文件完整性

```
$ md5sum -c *.md5
```



* 解压源码压缩包

```
$ tar -zxvf rk356x-android13-*.tar.gz
$ cd rk356x-android13-*
$ git reset --hard
```



### 编译完整镜像

根据CPU型号，选择对应编译命令；若编译失败，可尝试自行解决，或联系技术客服解决

**配置SDK编译项**

```
$ ./build.sh lunch

	will lunch sdk

	You're building on Linux
	Lunch menu...pick a combo:

	1. rk356x
	2. rk3588
	Which would you like? [0]:

	You're building on Linux
	Lunch menu...pick a combo:

	1. BoardConfig.mk
	2. BoardConfig-rk3566-kickpi-tx66.mk
	3. BoardConfig-rk3568-kickpi-k1a.mk
	4. BoardConfig-rk3568-kickpi-k1b.mk
```

>配置选择后，软件会配置成选择的设备；若切换设备需要重新配置，否则无需多次配置



**编译完整镜像**

```
$ ./build.sh -AUCKu
```

> 镜像生成目录：rockdev/Image-*/update-\*.img



可配置编译线程选项，指定线程32

```
$ ./build.sh -AUCKu -J32
```

> 注意：如果遇到编译Android时就失败了，但是没什么具体报错，大概率虚拟机配置过低；

或修改 build.sh 里面的默认配置后重新尝试

```diff
$ vim build.sh
-BUILD_JOBS=16
+BUILD_JOBS=1
$ ./build.sh -AUCKu
```



### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh -U
```

> 镜像生成目录：rockdev/Image-\*/uboot.img



**单独编译Kernel**

```
$ ./build.sh -CK
```

> 镜像生成目录：rockdev/Image-\*/boot.img



**单独编译Android**

```
$ ./build.sh -A
```

> 镜像生成目录：rockdev/Image-\*/super.img



**android 环境配置**

配置命令环境，可以使用 mm / get_build_var 等安卓环境下命令

```shell
(SDK)$ source build/envsetup.sh
```

设备配置选择

```
$ lunch
61
```

> 根据实际，选择对应的设备，比如'K1'和'K1B'为 rk3568

![2a41026b5899c13dcc725f59858abd7](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/2a41026b5899c13dcc725f59858abd7.png)

加载对应环境脚本

```
source device/rockchip/rk356x/BoardConfig-rk3568-kickpi-k1.mk 
```

环境配置成功，可以使用Android下相关命令，比如

```
$ get_build_var PRODUCT_HAVE_RKPHONE_FEATURES
true
```



### 其他常用命令

**内核配置**

```
$ ./build.sh -CKM
```

**编译多屏自适应镜像**<a id='multi'> </a>

```
$ ./build.sh -AUCKum
```

> 出厂Android/Linux镜像默认为多屏自适应镜像，兼容官方7寸/10寸/15寸显示屏，连接任一显示屏可直接点亮



## 常见问题

下面列举SDK编译过程中遇到的各种常见问题和解决方法或解决思路

### 配置不足

因内存配置不足，常见日志报错

* 出现**Killed** 

```
[hardware/rockchip/libgralloc/bifrost frameworks/native/include system/core/libs
ync system/core/libsync/include external/libdrm/include/drm] 33
Optee Version: v2
librknnrt disabled on RK3562
librknnrt disabled on RK3562
librknnrt disabled on RK3562
libeptz disabled on RK3562
libeptz disabled on RK3562
libeptz disabled on RK3562
[] 33
librockx disabled on RK3562
librockx disabled on RK3562
librockx disabled on RK3562
rk3562 WILL COMPILE rkaiq_tool_server binary
Killed
13:30:35 soong bootstrap failed with: exit status 1
ninja: build stopped: subcommand failed.

#### failed to build some targets (01:00:21 (hh:mm:ss)) ####

Build android failed!
```

* 解决方法：

增加实际内存，Android一般内存推荐32G，如果增加不了可以尝试，增加swap分区大小并且使用单线程编译看看

配置线程选项，指定单线程

```
$ ./build.sh -AUCKu -J1
```



### 编译出的镜像烧录后屏幕不亮

我们网盘提供的是多屏自适应镜像，需要特殊指令编译，参考[编译多屏自适应镜像](#multi)

如果你只是完整编译，需要自己去选择你使用的屏，参考[LCD介绍文档]()
