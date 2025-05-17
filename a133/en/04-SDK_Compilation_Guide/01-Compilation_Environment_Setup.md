# 01-Compilation_Environment_Setup

This build environment configuration applies to Android/Linux SDK.

### Hardware Requirements

| System Environment | Requirements       |
| ------------------- | ------------------ |
| OS Version          | Ubuntu 22.04       |
| CPU Cores           | 4 cores or more    |
| RAM                 | 16GB or more       |
| Storage             | 200GB or more      |

---

### Install Dependency Packages

```shell
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler 
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools
```

> Package names may vary depending on Ubuntu versions.  
> WSL environments are untested and not recommended.  
> For installation failures on different Ubuntu versions, search online for solutions.

---

## Common Issues

### Insufficient Configuration