# 41-Kernel 驱动配置

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
make ARCH=arm64 rockchip_defconfig //加载这个路径下的defconfig
```

make  saveconfig生成defconfig 需要cp 到SDK编译使用的*defconfig文件进行使用

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> 不同版本的defconfig可能不同，可自行在git历史记录中查找对应的版本文件



## dts

rk-linux 

```
kernel/arch/arm64/boot/dts/rockchip/
	rk3562-kickpi-k3-linux.dts
	rk3568-kickpi-k1-linux.dts
	rk3568-kickpi-k1b-linux.dts
```



## PWM 配置

例如：将K1拓展引脚上的GPIO配置成PWM

![image-20241206165454476](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20241206165454476.png)



