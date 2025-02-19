# 03-SDK Compilation



##  1. Compilation Environment Setup

This compilation environment configuration is suitable for Android/Linux SDK.

### Hardware Requirements

| System Environment | Requirements     |
| ------------------ | ---------------- |
| System Version     | Ubuntu 20.04     |
| CPU Cores          | 4 cores or above |
| Memory Capacity    | 16GB or above    |
| Disk Space         | 200GB or above   |



### Installing Dependency Packages

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

> Package names may vary depending on the version of Ubuntu.
>
> If installation fails on different Ubuntu versions, you can search online for corresponding solutions.



## 2. Compilation of Android SDK

### SDK Overview

Android SDK Version Information

Kernel Version: 5.4

Android System Version: 12.0



### Obtaining SDK Source Code

- Download Directory on Network Disk

```
1-SDK Software Source Code\Android12.0\
    h618-android12.0-20240320.tar.gz
```



- Copy the Source Code Archive to the Compilation Host Directory

  > Note:
  >
  > 1. Do not place the source code in a shared directory for compilation.
  > 2. Do not log in to the compilation host with the ROOT account.



- Verify File Integrity with MD5 Checksum

```
$ md5sum -c *.md5
```



- Merge Source Code Archives

```
$ cat *tar.gz_0* > sdk.tar.gz
```

> The original compressed package may exceed 100GB, so it is split into multiple parts for compression.
>
> Before extraction, the compressed packages need to be merged.



- Extract Source Code Archive

```
$ tar -zxvf *.tar.gz
```

> After extraction, if only a `.git` folder is visible in the source code directory, proceed to the next step to complete the restoration of the source code.



- Restore Source Code Directory with Git

```
$ git reset --hard
```



### Compilation of Complete Image

```
$ ./build.sh
```

> Image Generation Directory: longan/out/h618_android12_p2_uart0.img



### Compilation of Individual Partition Images

**Compile Uboot Only**

```
$ ./build.sh uboot
```

> Image Generation Directory: longan/out/h618_android12_p2_uart0.img



**Compile Kernel Only**

```
$ ./build.sh kernel
```

> Image Generation Directory: longan/out/h618_android12_p2_uart0.img



**Compile Android Only**

```
$ ./build.sh android
```

> Image Generation Directory: longan/out/h618_android12_p2_uart0.img



## 3. Compilation of Linux SDK

For Ubuntu 22.04 system, only image files are provided. Compilation can refer to ARMBIAN SDK.

### Download the SDK source code

* Download directory

```
1-SDK\Linux\
	h618-linux-20240320.tar.gz
```



* Copy the source code compression package to the compiled host directory

  > warning：
  >
  > 1. The source code should not be compiled in a shared directory
  >
  > 2. Compilation hosts are not allowed to log in with a ROOT account



* MD5校验文件完整性

```
$ md5sum -c *.md5
```



* MD5 checksum file integrity

```
$ tar -zxvf *.tar.gz
```

> After unzipping, you can see that there is only a .git folder in the source directory, and you need to perform the next step in order to restore the source code.



* Git recovery source code

```
$ git reset --hard
```





### Compile command

```
$ ./build.sh
```

Run`./build.sh` from the **aw-image-build** root to access the interactive build interface

![image-20241025181130073](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181130073.png)

It can be seen that there are a total of seven options on our function selection interface.

- Build all step ：Build a complete image and automatically execute step1 to step4.
- step1.Build Kernel：Compile the kernel separately
- step2.Build U-boot：Compile u-boot separately
- step3.Build base-rootfs and deb packages：Build a root file system image and package custom deb software packages.
- step4.Pack image：Package the parts of the system image into a complete image for burning according to rules. update source repository：Download or update the software source code
- clean source/build/out files：Clean up the pulled source code, files generated during the build process, and the final output image.

If we just need to build an image for board startup, select **Build all step** and press Enter to proceed to the next step

![image-20241025181221880](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181221880.png)

Select the board we want to mirror, such as **lubancat-a1 Allwinner H618**

![image-20241025181249872](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181249872.png)

Select the distribution and release versions of the root file system we want to build

![image-20241025181345456](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181345456.png)

Select whether the root file system we want to build is a desktop version, such as **desktop**

![image-20241025181414687](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181414687.png)

Select the desktop environment of the root file system we want to build, such as **Xfce**

![image-20241025181456150](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181456150.png)

Select the configuration file for the desktop environment of the root file system we want to build. By default, it is. **base configuration**

![image-20241025181509226](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181509226.png)

Select the root file system pre-installed package we want to build, select it with the space bar, and then press Enter to start the build process. After the build is complete, the image is saved in the **out/images** directory

### single-step build image

For users, if they want to conduct secondary development on the board and need to pre-install applications, modify and debug the kernel, etc., it may involve the separate construction of the root file system or kernel. At this time, the single-step construction method can be selected.

Run./build.sh from the aw-image-build root to access the interactive build interface

![image-20241025182302642](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025182302642.png)

You can see that in addition to using Build all step for one-click builds, there are also single-step build options from step1 to step4. In fact, the process of one-click builds is the process of calling these four scripts sequentially.

####  step1.Build Kernel

This step is to compile the kernel source code separately and generate the kernel deb package

Run./build.sh to enter the interactive build interface, select step1.Build Kernel and press Enter to proceed to the next step

![image-20241025183117623](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183117623.png)

Select the board on which we want to compile the kernel, such as **lubancat-a1 Allwinner H618**

![image-20241025183126142](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183126142.png)

Next, enter the kernel construction process. During the construction process, there will be some prompts for kernel construction

![image-20241025183152847](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183152847.png)

- Compiler kernel info：Compiled kernel version
- Compiler kernel path：Compiled kernel save path
- Copy kernel config：Kernel configuration file used
- Compiler kernel command：Kernel build command
- Compiler kernel deb：Building command for kernel deb package.

Some auxiliary information is also printed after the build is completed

![image-20241025183417053](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183417053.png)

- Kernel deb path：The save path of the kernel deb file generated by construction.
- Kernel deb name：Build generated kernel deb package name

If we make modifications to the kernel content, at this time, transfer the generated kernel deb package to the running board. Use dpkg -i linux-***.deb, and then restart the board to update and upgrade the kernel.

If we want to modify the kernel configuration file to enable or disable a feature of the kernel, what should we do?

First of all, we need to enter the directory where the kernel is located. You can check the prompt information during construction. **Compiler kernel path** enters the corresponding directory.

```
# Enter the kernel source directory 
cd /home/jiawen/Allwinner/H618/aw-image-build/source/kernel/linux-5.4-h618 

# Modify the kernel configuration file 
make ARCH=arm64 menuconfig

# After the modification is completed, save and exit the menuconfig configuration page. At this time, the #modified configuration is saved in .config. We also need to sync the modification to the kernel #configuration file of the board. 

# Save defconfig configuration file 
make ARCH=arm64 savedefconfig 

# Replace the board configuration file with the generated defconfig file 
cp defconfig arch/arm64/configs/linux_h618_defconfig 

# View the content of the board configuration file modification 
git diff arch/arm64/configs/linux_h618_defconfig
```

At this point, the modification of the board's kernel configuration file is complete, and we can recompile the kernel through the build tool

####  step2.Build U-boot

This step is to compile u-boot separately and package the generated u-boot image as a deb.

Run**./build.sh** to enter the interactive build interface, select **step2.Build** U-boot and press Enter to proceed to the next step

Select the board we want to compile u-boot on, such as **lubancat-a1 Allwinner H618**

If our board supports multiple versions of the kernel, in order to cooperate with the startup of the kernel, there will be different versions of u-boot. In this step, there will be a selection of compile branch versions, and the version consistent with the kernel branch will be selected. If only one version is supported, the default version will be used, and the selection page will not pop up.

Next, enter the u-boot compilation process, and there will be some build prompts during compilation

- Compiler uboot info：Compiled u-boot version
- Compiler uboot path：Compiled u-boot save path
- Compiler uboot config：The used u-boot configuration file
- Compiler uboot command：u-boot build command

Some auxiliary information will be printed after the end. 

- Target directory：Build the generated u-boot deb file save path
- File name：The name of the u-boot deb package generated by construction.

If we modify the content of the u-boot, then transfer the generated u-boot deb package to the running board, use `dpkg -i lubancat-a1-uboot-xxx deb` to unzip the u-boot image into the root file system of the board, and finally use a script to write the u-boot image into the specified location of our storage device to overwrite the original u-boot.

```
# Run the script with administrator privileges 
sudo nand-sata-install 
```

![image-20241025183525874](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183525874.png)

Select an option **5 Install/Update the bootloader on SD/EMMC **

![image-20241025183646097](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183646097.png)

Encounter warning selection yes

![image-20241025183611510](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183611510.png)

After completion, we can restart the board to use the updated u-boot

#### step3.Build base-rootfs and deb packages

This step involves building the basic root file system and locally customized deb packages separately

Run`./build.sh` to enter the interactive build interface, select step3.Build base-rootfs and deb packages and press Enter to proceed to the next step

Select the board on which we want to build the image, such as **lubancat-a1 Allwinner H618**

If our board supports multiple versions of the kernel, in order to adapt to some differences between different kernels, this step will also have the choice of **compilation ** branch version, choose the version consistent with the kernel branch, if only one version is supported, the default version will be used, and the selection page will not pop up.

At present, aw-image-build supports the construction of Debian system and Ubuntu system, and according to the different kernel versions, you can also choose different release versions, such as Debian system: Bullseye, bookworm, etc. Ubuntu systems: focal, jammy, etc

Then is to choose the type of the root file system we want to build, the server version is not a desktop version, suitable for small memory, small storage, high performance requirements for users, desktop is a desktop version, installed some commonly used desktop applications, occupy more resources, but the operation is more convenient, and the desktop PC operation is similar.

If the server version is selected, then the root filesystem build process begins;

If the desktop version is selected, further configuration is required.

Select the version of the desktop suite to install. We have selected the version suitable for installation based on the number of board resources configured

Select the configuration file of the desktop suite. The default is **base configuration**

Select additional pre-installed packages, which are optional. Due to the size of the published image, these additional packages are not installed by default in images shipped with the board

The next step is to build the root file system and some of the local deb packages. The build time is closely related to network performance and disk performance.

Because the construction of the root file system is time-consuming, in order to reduce the repeated construction of the root file system with the same content, we judge whether the root file system needs to be rebuilt by comparing the hash value of the name characters of the software package to be installed.

The basic root file system compression package built for the first time is stored in the **aw-image-build/build/rootfs-base** directory. **The naming rule is  system release version-Desktop suite version-processor architecture. Package string hash value. Compression format** , when we need to build the root file system twice, first calculate the string hash value of the name of all the software packages to be installed, and then find whether the corresponding compression package exists in the directory where the basic root file system compression package is stored. If it exists, skip the construction process and proceed directly to the next step. Build a new base root file system and compress it.

After the construction of the root file system is completed, it will continue to use the local source code to pre-build the custom deb packages to be installed into the system in stop4 and the deb packages to save the differences between the board.

Sometimes you will encounter the basic root file system content is damaged, need to forcibly rebuild the situation, in this case, in the **build/rootfs-base** directory with the corresponding name to delete all files and re-compile .

#### step4.Pack image

This step is to install and package the content generated by step1-step3 to generate a complete image for board burning. Run`./build.sh` to enter the interactive build interface, select step3.Build base-rootfs and deb packages and press Enter to proceed to the next step

