# 11-SDK source code compilation

[TOC]


## 1. build environment

This build environment configuration for Android/Linux SDK



### Hardware requirements

| System environment | request         |
| ------------------ | --------------- |
| System version     | > Ubuntu18.04   |
| CPU core count     | 4 cores or more |
| Memory capacity    | 16GB or more    |
| Hard disk capacity | 200GB or more   |



### Install dependent packages

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

> Package names change according to UBUNTU version updates
>
> The installation of different UBUNTU versions failed. You can search for the corresponding solution on the network.



### SDK introduction

Android SDK version information

Kernel version: 4.9

Android system version: 10.0



### Get the SDK source code

* Network disk download directory

```
├── 1-SDK软件源码
│   ├── android10.0
│   └── ubuntu1604
```

* Copy the source archive to the build host directory (Linux environment)

  > Notes:
  >
  > 1. The source code cannot be compiled in a shared directory
  >
  > 2. The compilation host cannot log in with the ROOT account.



* Unzip the source code compressed package, Git restores the source code directory

  >a133-*.tar.gz Refers to the decompressed code archive

```
mkdir sdk
tar -zxf a133-*.tar.gz -C sdk/
cd sdk
git reset --hard
```



## 2. Android SDK compilation



### Compile full image

```
$ ./build.sh
```

> Image generation directory: longan/out/a133_android10_c3_uart0.img



### Compile partition images separately

**Compile Uboot separately**

```
$ ./build.sh uboot
```

> Mirror generation directory: longan/out/a133_android10_c3_uart0.img



**Compiling Kernels Separately**

```
$ ./build.sh kernel
```

> Mirror generation directory: longan/out/a133_android10_c3_uart0.img



**Compile Android separately**

```
$ ./build.sh android
```

> Mirror generation directory: longan/out/a133_android10_c3_uart0.img



**kernel path**

```
longan/kernel/linux-4.9
```



### **defconfig **

path

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



### compilation problem

**Toolchain path error. Compilation requires configuration.**

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



**Due to different compilation environments (ld), it may cause an error in yylloc. The yylloc in the code needs to be modified.**

The actual path is modified according to the error prompt.

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



## 3.Linux SDK compilation

### full compilation

```
./build.sh 
```



mirror image

```
out/a133_linux_c3_uart0.img
```



If compilation fails, try configuring

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



### Compile separately

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



defconfig Modify and save

```
Make sure it is compiled once, or ./build.sh config
cd kernel/linux-4.9/
make ARCH=arm64 menuconfig
cd -
./build.sh saveconfig
```



### toolchain

packet path

```
build/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu.tar.xz
```

Path to the compiled tool

```
out/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu
```



