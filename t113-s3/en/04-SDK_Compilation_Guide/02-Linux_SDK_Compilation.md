# 02-Linux_SDK_Compilation


### Build Full Image  

**Initial Configuration**  
When running `build.sh` for the first time, select configurations (CPU model, board type, filesystem):  
```shell  
$ ./build.sh lunch  

======You are building t113 Linux======  
1. BoardConfig-t113-s3-kickpi-k4b-buildroot.mk  
2. BoardConfig-t113-s3-kickpi-k4b-ubuntu.mk  
Which board would you like (1-2):  
```  
> Run this command only once.  

**Compile Image**  
```shell  
./build.sh       # Compile  
./build.sh pack  # Generate image  
```  

> Output directory: `out/t113_linux_evb1_auto_uart0.img`  
> Contact support if compilation fails.  

### Build Partition Images Separately  

**Compile U-Boot**  
```shell  
$ ./build.sh uboot  
```  

**Compile Kernel**  
```shell  
$ ./build.sh kernel  
```  

**Compile Buildroot**  
```shell  
$ ./build.sh buildroot_rootfs  
```  

### Common Commands  

**Kernel Configuration**  
```shell  
$ ./build.sh menuconfig  
$ ./build.sh saveconfig  
```  
Default config: `device/config/chips/t113/configs/evb1_auto/linux-5.4/config-5.4`  

**Buildroot Configuration**  
```shell  
$ ./build.sh buildroot_menuconfig  
$ ./build.sh buildroot_saveconfig  
```  
Default config: `buildroot/buildroot-201902/configs/sun8iw20p1_t113_defconfig`  

### Toolchain Location  
After compilation, the toolchain is located at:  
```shell  
out/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_arm-linux-gnueabihf/bin  
```  
