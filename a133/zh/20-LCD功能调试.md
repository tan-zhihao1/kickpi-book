# 20-LCD功能调试



### 内核设备树

* 顶层设备树目录

```bash
$ vim longan/device/config/chips/a133/configs/c3/board.dts

// 放置于文件末尾
//#include "lcd-lvds-21-1920-1080.dtsi"
#include "lcd-lvds-7-1024-600.dtsi"
//#include "lcd-mipi-10-800-1280.dtsi"
```

```
linux 路径
device/config/chips/a133/configs/c3/board.dts
```



* 二级设备树目录

```
longan/kernel/linux-4.9/arch/arm64/boot/dts/sunxi/
	sun50iw10p1-pinctrl.dtsi
	lcd-mipi-10-800-1280.dtsi
	lcd-lvds-7-1024-600.dtsi
	lcd-lvds-21-1920-1080.dtsi
```

```
linux 路径
kernel/linux-4.9/arch/arm64/boot/dts/sunxi/
```



* 屏幕选择编译

  7寸 1024 * 600 LVDS 屏 选择 `#include "lcd-lvds-7-1024-600.dtsi"`

  1920 * 1080 DUAL LVDS 屏 选择 `#include "lcd-lvds-21-1920-1080.dtsi"`

如：选择7寸屏软件

```shell
$ vim longan/device/config/chips/a133/configs/c3/board.dts
#include "lcd-lvds-7-1024-600.dtsi"
//#include "lcd-lvds-21-1920-1080.dtsi"
//#include "lcd-mipi-10-800-1280.dtsi"
```

注意：lcd-*.dtsi ，同时只打开一个



### LCD驱动

* UBOOT

```
longan/brandy/brandy-2.0/u-boot-2018/drivers/video/sunxi/disp2/disp/lcd/
	mipi_800x1280.c			// MIPI LCD驱动
```

* KERNEL

```
longan/kernel/linux-4.9/drivers/video/fbdev/sunxi/disp2/disp/lcd/
	mipi_800x1280.c			// MIPI LCD驱动
```



## 调试信息
