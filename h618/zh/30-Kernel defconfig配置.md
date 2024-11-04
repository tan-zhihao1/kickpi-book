# 30-Kernel defconfig配置



## 配置defconfig

内核路径

```
longan/kernel/linux-5.4/
```



kernel config 配置相关命令

```
cd longan

# loadconfig|menuconfig|saveconfig|mergeconfig

./build.sh loadconfig
./build.sh menuconfig
./build.sh saveconfig
```



## 开启 DEBUG_FS

```
Kernel hacking > 
	Compile-time checks and compiler options >
		 [*] Debug Filesystem
```



