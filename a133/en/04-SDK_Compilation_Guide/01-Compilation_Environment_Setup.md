# 01-Compilation Environment Setup

## 1. Compilation Environment Setup

This configuration applies to both **Android/Linux SDK**.

---

### Hardware Requirements

| System Environment | Requirements                     |
| ------------------- | -------------------------------- |
| OS Version          | > Ubuntu 18.04 (Recommended: Ubuntu 22.04) |
| CPU Cores           | 4 cores or more                  |
| RAM                 | 16GB or more                     |
| Storage             | 200GB or more                    |

---

### Install Dependency Packages

```bash
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler mtd-utils
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools
```

> Notes:  
> - Package names may vary depending on Ubuntu versions.  
> - If installation fails on different Ubuntu versions, search online for solutions.  

---

### Obtain SDK Source Code

* **Network Disk Directory**:  
  ```
  ├── 1-SDK_Source_Code
  │   ├── android10.0
  │   └── ubuntu1604
  ```

* **Copy Source Code Archive to Compilation Host** (Linux environment):  
  > **Notes**:  
  > 1. Do not place source code in shared directories for compilation.  
  > 2. Do not use the **root account** to log into the compilation host.  

* **Extract Source Code and Restore Git Directory**:  
  > Replace `a133-*.tar.gz` with your actual source code archive.  
  ```bash
  mkdir sdk
  tar -zxf a133-*.tar.gz -C sdk/
  cd sdk
  git reset --hard
  ```