# Kernel

本文档主要介绍SDK的内核配置，怎么进行配置

## 快捷内核配置命令

Android 内核配置

```
$ ./build.sh -M
```

linux 内核配置

```
$ ./build.sh kernel-config
```

整个显示界面大致分为三部分，上方是操作的说明和图例说明。

- <Enter> 按下Enter键进入子菜单。
- <Y> 按下Y键，选中这项功能。
- <N> 按下N键，排除这项功能 。
- <M> 按下M键，以模块的形式选择。
- <Esc><Esc> 按两下Esc键，返回上一级菜单
- <?> 按下?键，查看高亮功能的帮助信息
- </> 按下/键，搜索
- [*] 被选中的项目
- [ ] 未被选择的项目
- <M> 以模块形式被选择的项目
- < > 未被选择的模块

## kernel通用配置

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





