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
