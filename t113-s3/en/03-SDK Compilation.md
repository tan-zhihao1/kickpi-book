# 03-SDK Compilation

## 1.  Setting up the Compilation Environment

This compilation environment setup is suitable for Linux SDK.

### Hardware Requirements
| System Environment   | Requirements          |
| ------ | ----------- |
| System Version   | Ubuntu18.04 |
| CPU Cores | 4 cores or more        |
| Memory Capacity   | 8GB or more       |
| Hard Disk Capacity   | 200GB or more     |

> After testing ubuntu22.04, there will be a lot of errors when compiling, so it is not recommended to use it.

### Installing Required Software Packages
``` shell

$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler 
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools

```

> The names of the software packages may vary depending on the version of Ubuntu.
>
> If the installation fails for a different Ubuntu version, you can search the internet for the corresponding solution.

## 2. Compiling the Linux SDK

### Obtaining the SDK Source Code

* SDK Directory on the Cloud Drive

``` 
1-SDK源码\linux\sdk\xxxxxxx\
	t113-linux-*.tar.gz
	t113-linux-*.md5
```



**Copy the source code compressed package to the compilation host directory**

> Precautions
>
> 1. If the compilation host is a virtual machine, you cannot compile in a shared directory.



**MD5 file integrity check**

```shell
$ md5sum -c *.md5
```



**Unzip the source code package**

```shell
$ tar -zxvf t113-linux-*.tar.gz
```

> The SDK source code compression package only contains the Git repository. You can use the ls -al command to view the .git folder.



**Restore source code files**

```shell
$ cd t113-linux
$ git reset --hard
```



### Get the Rootfs image

* File system network disk directory

```
1-SDK源码\linux\rootfs\
	buildroot-dl-*.tar.gz
	ubuntu-rootfs-*.tar.gz
```

* buildroot

The provided buildroot-dl-*.tar.gz compressed package can be used to compile the Buildroot file system offline, reducing the time to download the source code during compilation

Unzip buildroot-dl-*.tar.gz to the buildroot/buildroot-201902 directory

* ubuntu

ubuntu-rootfs-*.tar.gz is a compressed package of the ubuntu file system. It can be used with the SDK source code to compile and generate an ubuntu system image.

Copy ubuntu-rootfs-*.tar.gz to the device/config/rootfs_tar directory of the SDK and rename it to ubuntu-armhf.tar.gz




### Compile the complete image

When executing build.sh for the first time to compile the SDK, you must select the compilation configuration information, such as: CPU model, board model, file system model

For specific board model differences, please refer to `Getting Started Documentation`



* SDK compilation configuration

```shell
$ ./build.sh lunch

======you are building t113 linux======
1. BoardConfig-t113-s3-kickpi-k4b-buildroot.mk
2. BoardConfig-t113-s3-kickpi-k4b-ubuntu.mk
which board would you like (1-2): 
```

> The configuration command is compiled and executed once for the first time



* Compile the image

```shell
$ ./build.sh
$ ./build.sh pack
```

> Image generation directory: out/t113_linux_evb1_auto_uart0.img
>
> If the compilation fails, you can try to solve it yourself or contact technical support customer service to solve it



### Compile partition images separately

**build Uboot**

```shell
$ ./build.sh uboot
```



**build Kernel**

```shell
$ ./build.sh kernel
```



**build Buildroot**

```shell
$ ./build.sh buildroot_rootfs
```



### Other common commands

**Kernel Configuration**

```shell
$ ./build.sh menuconfig
$ ./build.sh saveconfig
```



**buildroot Configuration**

```shell
$ ./build.sh buildroot_menuconfig
$ ./build.sh buildroot_saveconfig
```

