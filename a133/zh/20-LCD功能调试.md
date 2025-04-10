# 20-LCD功能调试



## 内核设备树

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



## LCD驱动

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

