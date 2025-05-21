# LCD功能调试



适配自己的屏幕可参考网盘资料，也可联系业务申请定制

3-SoftwareData\SDK模块开发指南\Linux_LCD_开发指南.pdf

## 设备树文件

### 内核设备树

```bash
kernel/linux-5.4/arch/arm/boot/dts/t113-s3-kickpi-k4b.dts
此文件选择了生效的屏幕设备树文件只能选择一个，默认为7寸lvds屏幕 需选择和Uboot相同规格的设备树文件

//#include "t113-s3-kickpi-lcd-tv.dtsi"
#include "t113-s3-kickpi-lcd-lvds-7-1024-600.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-10-800-1280.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-8-800-1280.dtsi"

```

### Uboot设备树

```bash
t113-linux/brandy/brandy-2.0/u-boot-2018/arch/arm/dts/t113-s3-kickpi-k4b-uboot.dts
此文件选择了生效的屏幕设备树文件只能选择一个，默认为7寸lvds屏幕，需选择和内核相同规格的设备树文件

#include "t113-s3-kickpi-lcd-lvds-7-1024-600.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-10-800-1280.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-8-800-1280.dtsi"
//#include "t113-s3-kickpi-lcd-tv-out.dtsi"

```

## LCD驱动源码

* KERNEL

```shell
t113-linux/kernel/linux-5.4/drivers/video/fbdev/sunxi/disp2/disp/lcd
	panels.c			//7寸 LVDS LCD驱动
	mipi_8_800x1280.c	//8寸 MIPI LCD驱动
	mipi_10_800x1280.c	//10寸 MIPI LCD驱动
```

* UBOOT

```shell
t113-linux/brandy/brandy-2.0/u-boot-2018/drivers/video/sunxi/disp2/disp/lcd
	panels.c			//7寸 LVDS LCD驱动
	mipi_8_800x1280.c	//8寸 MIPI LCD驱动
	mipi_10_800x1280.c	//10寸 MIPI LCD驱动
```



## 调试信息

查看当前显示输出的属性

```shell
cat /sys/class/disp/disp/attr/sys 
```
