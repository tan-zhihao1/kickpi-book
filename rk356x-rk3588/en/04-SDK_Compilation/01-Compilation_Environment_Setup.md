# 01-Compilation_Environment_Setup


## 1. Compilation Environment Setup

This compilation environment configuration is applicable to Android/Linux SDK.

### Hardware Requirements

| System Environment | Requirements |
| --------- | ----------- |
| System Version | Ubuntu 22.04 |
| Number of CPU Cores | 4 cores or more |
| Memory Capacity | 16GB or more |
| Hard Disk Capacity | 200GB or more |


### Install Dependent Software Packages

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev libncurses5
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-gtk3-dev libgmp-dev libmpc-dev
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler 
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools
```

> The names of software packages may change with the updates of the UBUNTU version.
>
> If the installation fails on different UBUNTU versions, you can search for corresponding solutions online.
