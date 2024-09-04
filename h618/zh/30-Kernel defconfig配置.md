# 30-Kernel defconfig配置

## 配置defconfig

```
make ARCH=arm64 sun50iw9p1smp_h618_android_defconfig
make ARCH=arm64 menuconfig
make ARCH=arm64 saveconfig
```



## 开启 DEBUG_FS

```
Kernel hacking > 
	Compile-time checks and compiler options >
		 [*] Debug Filesystem
```



