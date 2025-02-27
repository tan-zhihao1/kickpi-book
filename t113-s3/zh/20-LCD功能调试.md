# LCD功能调试



## 内核设备树

```bash
kernel/linux-5.4/arch/arm/boot/dts/t113-s3-kickpi-k4b.dts
此文件选择了生效的屏幕设备树文件只能选择一个，默认为7寸lvds屏幕 需选择和Uboot相同规格的设备树文件

//#include "t113-s3-kickpi-lcd-tv.dtsi"
#include "t113-s3-kickpi-lcd-lvds-7-1024-600.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-10-800-1280.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-8-800-1280.dtsi"

```

Uboot设备树

```shell
t113-linux/brandy/brandy-2.0/u-boot-2018/arch/arm/dts/t113-s3-kickpi-k4b-uboot.dts
此文件选择了生效的屏幕设备树文件只能选择一个，默认为7寸lvds屏幕，需选择和内核相同规格的设备树文件

#include "t113-s3-kickpi-lcd-lvds-7-1024-600.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-10-800-1280.dtsi"
//#include "t113-s3-kickpi-lcd-mipi-8-800-1280.dtsi"
//#include "t113-s3-kickpi-lcd-tv-out.dtsi"

```

## LCD驱动

* UBOOT

```shell
t113-linux/brandy/brandy-2.0/u-boot-2018/drivers/video/sunxi/disp2/disp/lcd
	panels.c			//7寸 LVDS LCD驱动
	mipi_8_800x1280.c	//8寸 MIPI LCD驱动
	mipi_10_800x1280.c	//10寸 MIPI LCD驱动
```

* KERNEL

```
t113-linux/kernel/linux-5.4/drivers/video/fbdev/sunxi/disp2/disp/lcd
	panels.c			//7寸 LVDS LCD驱动
	mipi_8_800x1280.c	//8寸 MIPI LCD驱动
	mipi_10_800x1280.c	//10寸 MIPI LCD驱动
```



## 调试信息

查看当前显示输出的属性

```
cat /sys/class/disp/disp/attr/sys 
```

示例：

```
# cat /sys/class/disp/disp/attr/sys 
screen 0:
de_rate 300000000 hz, ref_fps:66
mgr0: 720x1280 fmt[rgb] cs[0x204] range[full] eotf[0x4] bits[8bits] err[1] force_sync[0] unblank direct_show[false] iommu[1]
dmabuf: cache[7] cache max[37] umap skip[0] overflow[4]
        lcd output      backlight(102)  fps:66.5         720x1280
        err:2   skip:99 irq:17699       vsync:12094     vsync_skip:0
   BUF    enable ch[3] lyr[0] z[0] prem[Y] a[globl 255] fmt[  1] fb[ 736,1280; 368, 640; 368, 640] crop[   0,   0, 720,1280] frame[   0,   0, 720,1280] addr[fec00000,fecf0000,fed29800] flags[0x       0] trd[0,0]
depth[ 0] disp[0]all:685, sub:685, cur:685, free:682, skip:0
```