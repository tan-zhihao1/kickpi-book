# 03-SDK Compilation






## 1.  Setting up the Compilation Environment

This compilation environment setup is suitable for Android/Linux SDK.

### Hardware Requirements

| System Environment   | Requirements          |
| ------ | ----------- |
| System Version   | Ubuntu20.04 |
| CPU Cores | 4 cores or more        |
| Memory Capacity   | 8GB or more       |
| Hard Disk Capacity   | 200GB or more     |



### Installing Required Software Packages

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

> The names of the software packages may vary depending on the version of Ubuntu.
>
> If the installation fails for a different Ubuntu version, you can search the internet for the corresponding solution.





## 2. Compiling the Linux SDK

### Obtaining the SDK Source Code

* SDK Directory on the Cloud Drive

```
1-SDK Source 软件源码\rk356x-linux\
	rk356x-linux-*.tar.gz
```



* Extracting the Source Code Archive

```
$ tar -zxvf rk356x-linux-*.tar.gz
$ git reset --hard
```

> The SDK source code only contains the Git repository, so the above commands are necessary to restore the source code.



### Obtaining the Rootfs Image

* File System Directory on the Cloud Drive

```
1-SDK\rk356x-linux\
	linaro-rootfs-20230524.tar.gz
	ubuntu-rootfs-20230523.tar.gz
```



* Extracting the Debian Image File

```
$ tar -zxvf linaro-rootfs-*.tar.gz
$ cp linaro-rootfs.img debian/

$ ls debian/linaro-rootfs.img
```

> The name and directory of the Debian image file must match.
>
> File System Directory: debian/linaro-rootfs.img



* Extracting the Ubuntu Image File

```
$ tar -zxvf ubuntu-rootfs-*.tar.gz
$ cp ubuntu-rootfs.img.img ubuntu/

$ ls ubuntu/ubuntu-rootfs.img
```

> The name and directory of the Ubuntu image file must match.
>
> File System Directory: ubuntu/ubuntu-rootfs.img



### Compiling the Complete Image

To compile the SDK for the first time, you must select the compilation configuration, such as CPU model, board model, and file system model.

Refer to the "Getting Started" documentation for specific board model differences.



* SDK Compilation Configuration

```
$ ./build.sh lunch
	
    You're building on Linux
    Lunch menu...pick a combo:
    1. rk356x
    2. rk3588
    Which would you like? [0]:	# Select the CPU model

    You're building on Linux
    Lunch menu...pick a combo:
	1. BoardConfig-rk3566-kickpi-tx66.mk
	2. BoardConfig-rk3568-kickpi-tx68.mk
    Which would you like? [0]:	# Select the board model

    You're building on Linux
    Lunch menu...pick a combo:
    0. default RootfsConfig.mk
    1. RootfsConfig-buildroot.mk
    2. RootfsConfig-debian.mk
    3. RootfsConfig-ubuntu.mk
    Which would you like? [0]:	# Select the file system model
```

> Note: Execute the build.sh script for SDK compilation for the first time



* Building the Image

```
$ ./build.sh
```

> Image Generation Directory: rockdev/update-*.img
>
> If the compilation fails, you can try to resolve it on your own or contact technical support for assistance.



### Compiling Individual Partition Images

**Uboot**

```
$ ./build.sh uboot
```

> Image Generation Directory：rockdev/uboot.img



**Kernel**

```
$ ./build.sh kernel
```

> Image Generation Directory：rockdev/boot.img



**Buildroot**

```
$ ./build.sh buildroot
```

> Image Generation Directory：rockdev/rootfs.img





## 3.  Compiling the Android SDK

### Obtaining the SDK Source Code

* Directory on the Cloud Drive

```
1-SDK Source 软件源码\rk356x-android13\
	rk356x-android13-*.tar.gz
```



* Extracting the Source Code Archive

```
$ tar -zxvf rk356x-android13-*.tar.gz
$ git reset --hard
```



### Compiling the Complete Image

Select the appropriate compilation command based on the CPU model.

If the compilation fails, you can try to resolve it on your own or contact technical support for assistance.



* RK3566 Platform

```
$ source build/envsetup.sh
$ lunch rk3566_t-userdebug
$ ./build.sh -AUCKu 
```

> Image Generation Directory：rockdev/Image-rk3566\_t/update-\*.img



* RK3568 Platform

```
$ source build/envsetup.sh
$ lunch rk3568_t-userdebug
$ ./build.sh -AUCKu
```

> Image Generation Directory：rockdev/Image-rk3568\_t/update-\*.img



### Compiling Individual Partition Images

**Uboot**

```
$ source build/envsetup.sh
$ lunch
$ ./build.sh -U
```

> Image Generation Directory：rockdev/Image-\*/uboot.img



**Kernel**

```
$ source build/envsetup.sh
$ lunch
$ ./build.sh -CK
```

> Image Generation Directory：rockdev/Image-\*/boot.img



**Android**

```
$ source build/envsetup.sh
$ lunch
$ ./build.sh -A
```

> Image Generation Directory：rockdev/Image-\*/super.img
