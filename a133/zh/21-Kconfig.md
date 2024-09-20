# 21-Kconfig

内核路径

```
longan/kernel/linux-4.9
```



kernel config 配置相关命令

```
cd longan

# loadconfig|menuconfig|saveconfig|mergeconfig

./build.sh loadconfig
./build.sh menuconfig
./build.sh saveconfig
./build.sh mergeconfig
```



## 开启 DEBUG_FS

```
Kernel hacking > 
	Compile-time checks and compiler options >
		 [*] Debug Filesystem
```

