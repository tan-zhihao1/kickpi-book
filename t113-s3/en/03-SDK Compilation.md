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

