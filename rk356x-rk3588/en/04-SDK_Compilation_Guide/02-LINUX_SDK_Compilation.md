# 02-LINUX_SDK_Compilation

### SDK Introduction

The Rockchip Linux SDK consists of an SDK source code package and a file system image.

The kernel version of the SDK source code package is 5.10, supporting Ubuntu 20.04 and Debian 11 file systems.

> The SDK provides a buildroot system, but **the buildroot system is not maintained**.

### Obtain the SDK Source Code

**Download the SDK source code compressed package from the network disk**

The network disk SDK directory is as follows:

```
1-SDK source code\rk356x-linux\
    rk356x-linux-*.tar.gz
    rk356x-linux-*.md5
```

**Copy the source code compressed package to the compilation host directory**

> Notes:
>
> 1. If the compilation host is a virtual machine, do not compile in the shared directory.

**Verify the integrity of the file using MD5**

```
$ md5sum -c *.md5
```

**Extract the source code compressed package**

```
$ tar -zxvf rk356x-linux-*.tar.gz

$ ls -al
    .git
```

> The SDK source code compressed package is shared via a Git repository. You can use the `ls -al` command to view the `.git` folder.

**Restore the source code files**

Enter the extracted directory and restore the source code using Git.

```
$ git reset --hard
```

> Note: Please operate in Linux and have Git installed.

### Obtain the File System Image

The SDK source code package does not include the file system image. You need to refer to the following steps to separately extract and copy the file system to the SDK source code.

The network disk provides Debian and Ubuntu file system images, which can be downloaded according to your needs.

**Network disk directory for the file system**

```
1-SDK source code\rk356x-linux\
    linaro-rootfs-20230524.tar.gz
    ubuntu-rootfs-20230523.tar.gz
    buildroot-dl-20230607.tar.gz
```

**Usage of `buildroot-dl-20230607.tar.gz`:**

```
$ tar -zxvf buildroot-dl-*.tar.gz 
$ cp -rf dl buildroot/

$ ls buildroot/dl
```

> `buildroot-dl-20230607.tar.gz` is an offline package of the dl library, which is used to save time during the first compilation.

**Verify the integrity of the file using MD5**

```
$ md5sum -c *.md5
```

**Extract the Debian image file**

```
$ tar -zxvf linaro-rootfs-*.tar.gz
$ cp linaro-rootfs.img debian/

$ ls debian/linaro-rootfs.img
```

> The name and directory of the Debian image file must be consistent.
>
> File system directory: `debian/linaro-rootfs.img`
>
> When selecting Debian during the `lunch` process, this `linaro-rootfs.img` will be used for compilation.

**Extract the Ubuntu image file**

```
$ tar -zxvf ubuntu-rootfs-*.tar.gz
$ cp ubuntu-rootfs.img ubuntu/

$ ls ubuntu/ubuntu-rootfs.img
```

> The name and directory of the Ubuntu image file must be consistent.
>
> File system directory: `ubuntu/ubuntu-rootfs.img`
>
> When selecting Ubuntu during the `lunch` process, this `ubuntu-rootfs.img` will be used for compilation.

### Compile the Complete Image

When executing `build.sh` for the first time to compile the SDK, you must select the compilation configuration information, such as CPU model, board model, and file system model.

The specific differences in board models can be found in the `Introduction to the Getting Started Document`.

**SDK Compilation Configuration**

```
$ ./build.sh lunch

Pick a chip:

1. rk3566_rk3568
2. rk3588
Which would you like? [1]: 1
Switching to chip: rk3566_rk3568
Pick a defconfig:

1. rockchip_defconfig
2. rockchip_rk3562_kickpi_k3_buildroot_defconfig
3. rockchip_rk3562_kickpi_k3_debian_defconfig
4. rockchip_rk3562_kickpi_k3_ubuntu_defconfig
5. rockchip_rk3568_kickpi_k1_buildroot_defconfig
6. rockchip_rk3568_kickpi_k1_debian_defconfig
7. rockchip_rk3568_kickpi_k1_ubuntu_defconfig
8. rockchip_rk3568_kickpi_k1b_buildroot_defconfig
9. rockchip_rk3568_kickpi_k1b_debian_defconfig
10. rockchip_rk3568_kickpi_k1b_ubuntu_defconfig
Which would you like? [1]: 
```

> The configuration command only needs to be executed once during the first compilation.

**Compile the Image**

```
$ ./build.sh
```

> Image generation directory: `rockdev/update-*.img`
>
> If the compilation fails, you can try to solve it by yourself or contact the technical support customer service.

### Compile Partition Images Separately

**Compile Uboot Separately**

```
$ ./build.sh uboot
```

> Image generation directory: `rockdev/uboot.img`

**Compile the Kernel Separately**

```
$ ./build.sh kernel
```

> Image generation directory: `rockdev/boot.img`

**Compile Buildroot Separately**

```
$ ./build.sh buildroot
```

> Image generation directory: `rockdev/rootfs.img`

### Other Commonly Used Commands

**Kernel Configuration**

```
$ ./build.sh kernel-config
```

**Buildroot Configuration**

```
$ ./build.sh menuconfig
```

**Compile the Multi-Screen Adaptive Image**

```
$ ./build.sh all_multi_dtb
```

> The factory Android/Linux image is a multi-screen adaptive image by default, compatible with the official 7-inch/10-inch/15-inch displays. It can be directly lit up when connected to any display.

### New Compilation Items in the SDK

**Example: Add a compilation option for RK3568 K1 Buildroot**

* Add a new configuration file

```
$ cd device/rockchip/rk3566_rk3568/
$ cp rockchip_rk3568_kickpi_k1_debian_defconfig rockchip_rk3568_kickpi_k1_buildroot_defconfig
```

* Modify the compilation configuration file

```
$ vim rockchip_rk3568_kickpi_k1_buildroot_defconfig

- RK_ROOTFS_SYSTEM_DEBIAN=y
+ RK_ROOTFS_SYSTEM_BUILDROOT=y
```

### Toolchain

```
prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```