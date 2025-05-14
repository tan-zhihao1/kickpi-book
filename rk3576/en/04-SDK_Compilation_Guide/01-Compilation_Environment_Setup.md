# 01-Compilation_Environment_Setup

## Build Environment Setup

This build environment configuration is applicable to Android/Linux SDK.

### Hardware Requirements

| System Environment | Requirements |
| --------- | ----------------- |
| System Version | Ubuntu 22.04, 64-bit |
| Number of CPU Cores | More than 4 cores |
| Memory Capacity | More than 32GB |
| Hard Disk Capacity | More than 500GB |

### Install Dependent Software Packages

```
sudo apt update
sudo apt install autoconf bc binfmt-support bison build-essential bzip2
sudo apt install chrpath cmake cpp-aarch64-linux-gnu curl device-tree-compiler diffstat
sudo apt install expat expect expect-dev fakeroot flex
sudo apt install g++ g++-multilib gawk gcc gcc-multilib git gnupg gperf gpgv2 imagemagick
sudo apt install lib32ncurses5-dev lib32readline-dev lib32z1-dev libgmp-dev 
sudo apt install libgucharmap-2-90-dev liblz4-tool libmpc-dev
sudo apt install libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-gtk3-dev 
sudo apt install libxml2 libxml2-utils live-build lzop
sudo apt install make module-assistant ncurses-dev openjdk-8-jdk 
sudo apt install patchelf pngcrush python2 python-is-python3 python-pip
sudo apt install qemu-user-static rsync schedtool squashfs-tools ssh sudo 
sudo apt install texinfo u-boot-tools unzip
sudo apt install xsltproc yasm zip zlib1g-dev pip
sudo pip install pyelftools
```

> The names of software packages may change with the update of the UBUNTU version.
>
> If the installation fails on different UBUNTU versions, you can search the Internet for corresponding solutions.

## Special Environment Setup for LINUX SDK

**Configure the environment for compiling Debian**

```
$ sudo dpkg -i debian/ubuntu-build-service/packages/*
$ sudo apt-get install -f
```

**Configure the environment for compiling Ubuntu**

```
$ sudo apt-get install binfmt-support qemu-user-static --reinstall
```
