# Kernel

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

kernel config 通用配置命令

```
cd kernel-5.10/
make loadconfig
make ARCH=arm64 menuconfig
make saveconfig
make ARCH=arm64 rockchip_defconfig
```
> ARCH=arm64&emsp;配置架构，目前rk356x/3588为arm64
> menuconfig&emsp;启动图形化配置界面
> saveconfig&emsp;将当前配置精简后保存为 `defconfig` 文件‌
> rockchip_defconfig&emsp;加载 Rockchip 官方预置的 ARM64 架构配置文件
> loadconfig&emsp;加载默认的defconfig

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> make  saveconfig生成defconfig 需要cp 到SDK编译使用的*defconfig文件进行使用
>
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
	rk3588-kickpi-k8-linux.dts
```

rk-android

```
kernel-5.10/arch/arm64/boot/dts/rockchip
 rk3568-kickpi-k1-android.dts
 rk3568-kickpi-k1b-android.dts
 rk3562-kickpi-k3-android.dts
 rk3588-kickpi-k8-android.dts
```





