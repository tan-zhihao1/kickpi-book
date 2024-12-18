# 20-LCD function debugging



### Kernel Device Tree

* top-level device tree directory

```bash
$ vim longan/device/config/chips/a133/configs/c3/board.dts

// Place at the end of the file
//#include "lcd-lvds-21-1920-1080.dtsi"
#include "lcd-lvds-7-1024-600.dtsi"
//#include "lcd-mipi-10-800-1280.dtsi"
```

```
linux path
device/config/chips/a133/configs/c3/board.dts
```



* secondary device tree directory

```
longan/kernel/linux-4.9/arch/arm64/boot/dts/sunxi/
	sun50iw10p1-pinctrl.dtsi
	lcd-mipi-10-800-1280.dtsi
	lcd-lvds-7-1024-600.dtsi
	lcd-lvds-21-1920-1080.dtsi
```

```
linux path
kernel/linux-4.9/arch/arm64/boot/dts/sunxi/
```



* screen select compile

  7 inch 1024 * 600 LVDS screen, select `#include "lcd-lvds-7-1024-600.dtsi"`

  1920 * 1080 DUAL LVDS screen, select `#include "lcd-lvds-21-1920-1080.dtsi"`

For example: choose 7-inch screen software

```shell
$ vim longan/device/config/chips/a133/configs/c3/board.dts
#include "lcd-lvds-7-1024-600.dtsi"
//#include "lcd-lvds-21-1920-1080.dtsi"
//#include "lcd-mipi-10-800-1280.dtsi"
```

Attention: lcd- * .dtsi, open only one at a time



### LCD driver

* UBOOT

```
longan/brandy/brandy-2.0/u-boot-2018/drivers/video/sunxi/disp2/disp/lcd/
	mipi_800x1280.c			// MIPI LCD driver
```

* KERNEL

```
longan/kernel/linux-4.9/drivers/video/fbdev/sunxi/disp2/disp/lcd/
	mipi_800x1280.c			// MIPI LCD driver
```



