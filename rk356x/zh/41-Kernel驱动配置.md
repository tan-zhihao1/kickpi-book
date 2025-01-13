# 41-Kernel 驱动配置

## 配置defconfig

Android 内核配置

```
$ ./build.sh -M
```

linux 内核配置

```
$ ./build.sh kernel-config
```

内核路径

```
kernel-5.10
```

kernel config 命令通用配置

```
cd kernel-5.10/
make ARCH=arm64 menuconfig //根据IC架构，目前rk356x/3588选用arm64
//主要命令
make loadconfig //恢复旧的
make menuconfig //进入配置图形化界面
make saveconfig //保存为默认配置
make ARCH=arm64 rockchip_defconfig //加载这个路径下的defconfig
```

> 具体配置可通过 build.sh 进行追踪

make  saveconfig生成defconfig 需要cp 到SDK编译使用的*defconfig文件进行使用

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> 不同版本的defconfig可能不同，可在git历史记录中查找对应的版本文件
>
> $ git log --name-only   kernel-5.10/ | grep defconfig



## dts

rk356x-linux

```
kernel/arch/arm64/boot/dts/rockchip/
	rk3562-kickpi-k3-linux.dts
	rk3568-kickpi-k1-linux.dts
	rk3568-kickpi-k1b-linux.dts
	 rk3588-kickpi-k8-android.dts
```

rk-android

```
kernel-5.10/arch/arm64/boot/dts/rockchip
 rk3568-kickpi-k1-android.dts
 rk3568-kickpi-k1b-android.dts
 rk3562-kickpi-k3-android.dts
 rk3588-kickpi-k8-android.dts
```



## PWM 配置

例如：将K1拓展引脚上的GPIO配置成PWM





