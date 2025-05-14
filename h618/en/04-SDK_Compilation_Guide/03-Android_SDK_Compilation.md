# 03-Android_SDK_Compilation

### SDK Overview

**Version Information**  
- Kernel version: 5.4  
- Android OS version: 12.0  

---

### Obtaining the SDK Source Code

* **Network Disk Download Directory**:  
```shell
1-SDK\Android12.0\
	h618-android12.0-xxxxxxxxxx.tar.gz
# Download the latest dated version.
```

* **Copy the source code archive to the compilation host**:  
  > Notes:  
  > 1. Do not compile the source code in a shared directory.  
  > 2. Do not use the ROOT account to log in to the compilation host.  

* **Verify file integrity with MD5**:  
```
$ md5sum -c *.md5
```

* **Extract the source code archive**:  
```
$ tar -zxvf *.tar.gz
```
> After extraction, only the `.git` folder will be visible. Proceed to the next step to fully restore the source code.  

* **Restore the source code using Git**:  
```
$ git reset --hard
```

---

### Initial Compilation Environment Setup

First-time compilation requires environment configuration. Skip this step if the SDK location remains unchanged.  

```shell
# Navigate to the "longan" directory
$ cd longan
# Execute the configuration command
$ ./build.sh config
```

Select the following configurations:  
```
Welcome to mkscript setup progress
All available platform:
   0. android
   1. linux
Choice [android]: 0
All available ic:
   0. h618
Choice [h618]: 0
All available board:
   0. ft
   1. p1
   2. p2
   3. p7
   4. p7l
   5. perf1
   6. perf2
   7. perf3
   8. qa
Choice [p2]: 2
All available flash:
   0. default
   1. nor
Choice [default]: 0
All available kern_ver:
   0. linux-5.4
Choice [linux-5.4]: 0
All available arch:
   0. arm
   1. arm64
Choice [arm64]: 1
```

Return to the previous directory:  
```
$ cd -
```

**Select the Target Board**:  
```
$ ./build.sh lunch
will lunch sdk

You're building on Linux
Lunch menu...pick a combo:

1. BoardConfig-kickpi-k2b-tablet.mk
2. BoardConfig-kickpi-k2b-tv.mk
3. BoardConfig-kickpi-k2c-tablet.mk
4. BoardConfig-kickpi-k2c-tv.mk

Which would you like? [0]:
```

> **Note**:  
> `BoardConfig-kickpi-'board'-'desktop'`  
> - `board`: Options include `k2b` / `k2c`  
> - `desktop`: Options include `tv` / `tablet`  

---

### Compile the Full Image

```
$ ./build.sh
```
> **Output directory**: `longan/out/h618_android12_p2_uart0.img`  

> **Troubleshooting**:  
> If the Android compilation fails without clear errors, reduce the parallel build processes in `build.sh` (e.g., change `make -j32` to `make -j8` based on your hardware).  

---

### Compile Partition Images Separately

**Compile U-Boot Only**:  
```
$ ./build.sh uboot
```
> **Output directory**: `longan/out/h618_android12_p2_uart0.img`  

**Compile Kernel Only**:  
```
$ ./build.sh kernel
```
> **Output directory**: `longan/out/h618_android12_p2_uart0.img`  

**Compile Android Only**:  
```
$ ./build.sh android
```
> **Output directory**: `longan/out/h618_android12_p2_uart0.img`  

---

### Configure the Kernel

```
cd longan
./build.sh menuconfig
```

---

### Notes
- Ensure the compilation environment has sufficient disk space and memory.  
- Use `git pull` regularly to update the SDK source code.  
- Generated images can be flashed to the target device using tools like `PhoenixSuit` or `Fastboot`.