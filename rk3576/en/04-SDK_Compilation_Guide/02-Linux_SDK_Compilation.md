# 02-Linux_SDK_Compilation

## SDK Introduction

Kernel version: 6.1

Debian system version: 12

Ubuntu system version: 2404

## Obtain the SDK Source Code

* Network disk directory

```
├── 1-SDK
│   └── Linux
│       ├── rootfs
│       │   ├── linaro-rootfs-*.img
│       │   └── ubuntu-rootfs-*.img
│       └── sdk
│           ├── rk3576-linux-*.md5sum
│           └── rk3576-linux-*.tar.gz
```

> *Here, * generally refers to the date. The same applies below. The actual names shall be subject to the network disk.

1. Copy the source code compressed package to the compilation host directory (if the compilation host is a virtual machine, do not compile it in the shared directory).

2. Verify the integrity of the file using MD5.

```
md5sum rk3576-linux-*.tar.gz
```

> If the result is consistent with the content of rk3576-linux-*.md5sum, the file is downloaded completely.

3. Extract the source code compressed package.

```
$ mkdir rk3576-linux
$ tar -zxvf rk3576-linux-*.tar.gz -C rk3576-linux/
$ cd rk3576-linux/
$ git reset --hard
```

## Configure the Environment

> Prerequisite: Ensure the compilation hardware requirements are met and the [Compilation Environment Setup](01-Compilation_Environment_Setup.md) is successful.

Configure the environment before individual compilation or full compilation.

```
$ ./build.sh lunch
Log colors: message notice warning error fatal

Log saved at /home/huangcm/A/sdk/rk3576/rk3576-linux/output/sessions/2024-12-05_16-25-44
Pick a defconfig:

1. rockchip_defconfig
2. rockchip_rk3576_kickpi_k7_buildroot_defconfig
3. rockchip_rk3576_kickpi_k7_debian_defconfig
4. rockchip_rk3576_kickpi_k7_ubuntu_defconfig
Which would you like? [1]:
```

> Select the corresponding motherboard and system as needed.
>
> Debian uses the debian/linaro-rootfs.img image by default. If it does not exist, it will be compiled and generated.
>
> Ubuntu uses the ubuntu/ubuntu2204-rootfs.img image by default. If it does not exist, it will be compiled and generated.
>
> If you need to quickly compile Debian, you can use the linaro-rootfs-*.img in the network disk, rename it and save it to debian/linaro-rootfs.img.
>
> If you need to quickly compile Ubuntu, you can use the ubuntu2404-rootfs-*.img in the network disk, rename it and save it to ubuntu/ubuntu-rootfs.img.

### Full Compilation

Standard image

```
$ ./build.sh 
```

Multi-screen recognition image

```
$ ./build.sh all_multi_dtb
```

### Individual Compilation

**Kernel defconfig Configuration**

```
$ ./build.sh kernel-config
```

**U-Boot Compilation**

```
$ ./build.sh uboot
```

**Kernel Compilation**

Method 1

```
$ ./build.sh kernel
```

Method 2

```
$ export CROSS_COMPILE=../prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-
$ make ARCH=arm64 rockchip_linux_defconfig rk3576.config
$ make ARCH=arm64 rk3576-kickpi-k7-linux.img -j24
```

**Kernel Multi-screen Recognition Compilation**

```
$ ./build.sh kernel_multi_dtb
```

**Debian Compilation**

Configure the environment

```
$ sudo dpkg -i debian/ubuntu-build-service/packages/*
$ sudo apt-get install -f
```

Compile linaro-rootfs.img

```
$ ./build.sh debian
```

> If debian/linaro-rootfs.img does not exist, it will be generated through compilation using this command.

**Ubuntu Compilation**

Configure the environment

```
$ sudo apt-get install binfmt-support qemu-user-static --reinstall
```

Compile linaro-rootfs.img

```
$ ./build.sh ubuntu
```

> If ubuntu/ubuntu-rootfs.img does not exist, it will be generated through compilation using this command.

### Other Notes

**Firmware Description**

The following files will be generated after a full compilation:

```
rockdev
├── boot.img
├── MiniLoaderAll.bin
├── misc.img
├── oem.img 
├── parameter.txt 
├── recovery.img 
├── rootfs.img 
├── uboot.img 
├── update.img 
├── update-rk3576-kickpi-k7-linux-*-*.img 
└── userdata.img 
```

The image to be flashed is **SDK/rockdev/update-rk3576-kickpi-k7-linux-*-*.img**.

**DTS Path**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-linux.dts
```

**Defconfig Path**

```
kernel-6.1/arch/arm64/configs/rockchip_linux_defconfig
```

**U-Boot Defconfig Path**

```
u-boot/configs/rk3576_defconfig
```

**U-Boot Defconfig Configuration**

```
$ cd u-boot/
$ make rk3576_defconfig 
$ make menuconfig
$ make savedefconfig
$ cp defconfig configs/rk3576_defconfig
```