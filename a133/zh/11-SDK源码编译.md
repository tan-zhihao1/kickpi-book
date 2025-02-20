# 11-SDK源码编译




## 1. 编译环境搭建

此编译环境配置 适用 Android/Linux SDK



### 硬件要求

| 系统环境  | 要求          |
| --------- | ------------- |
| 系统版本  | > Ubuntu18.04 |
| CPU核心数 | 4核以上       |
| 内存容量  | 16GB以上      |
| 硬盘容量  | 200GB以上     |



### 安装依赖软件包

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler mtd-utils
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools
```

> 软件包名称 会根据UBUNTU版本更新而变化
>
> 不同UBUNTU版本安装失败，可网络搜索对应的解决方法



### SDK介绍

Android SDK版本信息

内核版本：4.9

Android系统版本：10.0



### 获取SDK源码

* 网盘下载目录

```
├── 1-SDK软件源码
│   ├── android10.0
│   └── ubuntu1604
```

* 拷贝源码压缩包到编译主机目录 （linux环境）

  > 注意事项：
  >
  > 1. 源码不可放在共享目录下编译
  >
  > 2. 编译主机不可用ROOT账号登陆



* 解压源码压缩包，Git恢复源码目录

  >a133-*.tar.gz 指代解压的代码压缩包

```
mkdir sdk
tar -zxf a133-*.tar.gz -C sdk/
cd sdk
git reset --hard
```



## 2. Android SDK编译



### 编译完整镜像

```
$ cd longan/
$ ./build.sh config

Welcome to mkscript setup progress
All available platform:
   0. android
   1. linux
Choice [android]: 0
All available ic:
   0. a133
   1. t509
Choice [a133]: 0
All available board:
   0. b1
   1. b3
   2. b4
   3. c3
   4. c4
   5. dpf
   6. fpga
   7. perf1
   8. perf2
   9. perf3
  10. qa
  11. ver
Choice [c3]: 3
```

```
$ cd - 
$ ./build.sh lunch //根据板型选择K5或K5C
$ ./build.sh
```

> 镜像生成目录：longan/out/kickpi-*_a133_android10_c3_uart0.img



### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh uboot
```

> 镜像生成目录：longan/out/kickpi-*_a133_android10_c3_uart0.img



**单独编译Kernel**

```
$ ./build.sh kernel
```

> 镜像生成目录：longan/out/kickpi-*_a133_android10_c3_uart0.img



**单独编译Android**

```
$ ./build.sh android
```

> 镜像生成目录：longan/out/kickpi-*_a133_android10_c3_uart0.img



**内核路径**

```
longan/kernel/linux-4.9
```



### **defconfig **

路径

```
longan/kernel/linux-4.9/arch/arm64/configs/sun50iw10p1smp_a133_android_defconfig
```



```
cd longan

# loadconfig|menuconfig|saveconfig|mergeconfig

./build.sh loadconfig
./build.sh menuconfig
./build.sh saveconfig
./build.sh mergeconfig
```







**由于编译环境 ld 不同，可能会导致 yylloc 报错，需要修改代码中的yylloc**

实际路径根据报错提示修改

```diff
--- a/longan/kernel/linux-4.9/scripts/dtc/dtc-lexer.lex.c
+++ b/longan/kernel/linux-4.9/scripts/dtc/dtc-lexer.lex.c
@@ -631,8 +631,8 @@ char *yytext;
 #include "srcpos.h"
 #include "dtc-parser.tab.h"

-//YYLTYPE yylloc;
-extern YYLTYPE yylloc;
+YYLTYPE yylloc;
+//extern YYLTYPE yylloc;
```



## 3.Linux SDK编译

### 完整编译

```
./build.sh 
```

>镜像生成目录：out/a133_linux_c3_uart0.img



### 单独编译

kernel

```
./build.sh kernel
```



rootfs

```
./build.sh rootfs
```



### defconfig 

```
kernel/linux-4.9/arch/arm64/configs/sun50iw10p1smp_defconfig
```



defconfig 修改及保存

```
确保编译过一次 或 ./build.sh config
cd kernel/linux-4.9/
make ARCH=arm64 menuconfig
cd -
./build.sh saveconfig
```



### 编译问题

如果编译失败，尝试配置

```
$ ./build.sh config

Welcome to mkscript setup progress
All available platform:
   0. android
   1. linux
Choice [android]: 1
All available linux_dev:
   0. bsp
   1. dragonboard
Choice [bsp]: 0
All available kern_ver:
   0. linux-4.9
Choice [linux-4.9]: 0
All available ic:
   0. a133
Choice [a133]: 0
All available board:
   0. b1
   1. b3
   2. c3
   3. c4
   4. fpga
   5. perf1
   6. perf2
   7. perf3
   8. qa
   9. ver
Choice [c3]: 2
All available flash:
   0. default
   1. nor
Choice [default]: 0
```



### 工具链

包路径

```
build/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu.tar.xz
```

编译后工具的路径

```
out/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu
```



