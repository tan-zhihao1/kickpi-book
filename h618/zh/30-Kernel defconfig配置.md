# 30-Kernel defconfig配置

## Android配置defconfig

内核路径

```
longan/kernel/linux-5.4/
```



kernel config 配置相关命令

```
cd longan

# loadconfig|menuconfig|saveconfig|mergeconfig

// 导入defconfig
./build.sh loadconfig

// 打开menuconfig进行配置
./build.sh menuconfig

// 保存defconfig
./build.sh saveconfig
```



## 开启 DEBUG_FS

```
Kernel hacking > 
	Compile-time checks and compiler options >
		 [*] Debug Filesystem
```



