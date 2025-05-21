# 01-Compilation_Environment_Setup

This configuration applies to the Linux SDK.  

### Hardware Requirements  

| **Environment** | **Requirements**       |  
|------------------|------------------------|  
| OS Version       | Ubuntu 18.04           |  
| CPU Cores        | 4 cores or more        |  
| RAM              | 16GB or more           |  
| Storage          | 200GB or more          |  

> **Notes**:  
> - Ubuntu 22.04/20.04 may cause compilation errors and is not recommended.  
> - WSL environments are untested and not supported.  

### Install Dependencies  

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

> Package names may vary across Ubuntu versions. Search online for solutions if installation fails.  

## 2. Software Preparation  

### SDK Overview  
Tina5.0 Linux SDK supports two filesystems: **Buildroot** and **Ubuntu**.  

### Obtain SDK Source Code  

**Download SDK from Cloud Storage**  
Cloud storage directory:  
```  
1-SDK/linux/sdk/xxxxxxx/  
    t113-linux-*.tar.gz  
    t113-linux-*.md5  
```

**Copy SDK to Build Host**  
> **Important**: Do not place the SDK in shared folders if using a VM.  

**Verify File Integrity**  
```shell  
$ md5sum -c *.md5  
```

**Extract SDK**  
```shell  
$ tar -zxvf t113-linux-*.tar.gz  
```

> **Warning**: Do NOT use root privileges! The SDK contains Git repositories (check `.git` with `ls -al`).  

**Initialize Source Code**  
```shell  
$ cd t113-linux  
$ git reset --hard  
```
> **Warning**: Do NOT use root privileges!  

### Obtain Rootfs Images  

**Filesystem Directory in Cloud Storage**:  
```  
1-SDK/linux/rootfs/  
    buildroot-dl-*.tar.gz  
    ubuntu-rootfs-*.tar.gz  
```

- **Buildroot Filesystem**  
  Extract `buildroot-dl-*.tar.gz` to `buildroot/buildroot-201902/`.  

- **Ubuntu Filesystem**  
  Copy `ubuntu-rootfs-*.tar.gz` to `device/config/rootfs_tar/` and rename it to `ubuntu-armhf.tar.gz`.  

