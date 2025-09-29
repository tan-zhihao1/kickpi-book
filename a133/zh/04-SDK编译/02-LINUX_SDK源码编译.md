# 02-LINUX_SDK源码编译

## 完整编译

**配置环境**

```shell
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

**选择板卡**

```
$ ./build.sh lunch 
```

> 根据需要选择 k5 或 k5c

**编译完整镜像**


```
$ ./build.sh 
```



## 单独编译

单编 kernel

```
$ ./build.sh kernel
```

单编 rootfs

```
$ ./build.sh rootfs
```



## Kconfig配置

kconfig 修改及保存

```
$ ./build.sh config
$ cd kernel/linux-4.9/
$ make ARCH=arm64 menuconfig
$ cd -
$ ./build.sh saveconfig
```

命令集成

```
$ ./build.sh menuconfig
```

>如果命令未支持，自行添加添加补丁；
>
>```diff
>--- a/build.sh
>+++ b/build.sh
>
>+function build_menuconfig()
>+{
>+    build/mkcommon.sh autoconfig  -o bsp -n default -i a133 -b c3 -k linux-4.9
>+    cd kernel/linux-4.9/
>+    make ARCH=arm64 menuconfig
>+    cd -
>+    build/mkcommon.sh saveconfig
>+}
>+
> if [ x"$1" == "xlunch" ] ;then
>     build_lunch && exit 0
>+elif [ x"$1" == "xmenuconfig" ] ;then
>+    build_menuconfig 
>+    exit 0;
> else
>```

## 设备树路径

``` 
kickpi-k5c.dts/device/config/chips/a133/configs/c3/kickpi-k5c.dts
```



## 工具链

包路径

```
build/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu.tar.xz
```

编译后工具的路径

```
out/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu
```



## 常见问题

### 提示缺少 event.c 文件问题

```
aarch64-linux-gnu-gcc: error: /home/A/sdk/a133/a133-linux-test1/kernel/linux-4.9/modules/gpu/img-rgx/linux/rogue_km/binary_sunxi_linux_release/target_aarch64/kbuild/services/server/env/linux/event.c: No such file or directory
```

需要进行清除操作

```
$ ./build.sh clean
```

