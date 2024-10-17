# 41-Kernel defconfig配置

## 配置defconfig

内核路径

```
kernel-5.10
```

kernel config 配置相关命令

```
cd kernel-5.10/
make ARCH=arm64 menuconfig //我们默认使用arm64
//主要命令
make loadconfig //恢复旧的
make menuconfig //进入配置图形化界面
make saveconfig //保存为默认配置
```

make  saveconfig生成defconfig 需要cp 到SDK编译使用的*defconfig文件进行使用

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> 不同版本的defconfig可能不同，可自行在git历史记录中查找对应的版本文件



