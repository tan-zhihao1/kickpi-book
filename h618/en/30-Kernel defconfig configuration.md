# 30-Kernel defconfig configuration

## Android configuration  deconfig

kernel path

```
longan/kernel/linux-5.4/
```



Kernel config configuration related commands

```
cd longan

# loadconfig|menuconfig|saveconfig|mergeconfig

// Import defconfig
./build.sh loadconfig

// Open menuconfig for configuration
./build.sh menuconfig

// save defconfig
./build.sh saveconfig
```



## open DEBUG_FS

```
Kernel hacking > 
	Compile-time checks and compiler options >
		 [*] Debug Filesystem
```



