# LCD


To adapt your own screen, refer to the cloud storage resources or contact sales for customization: 

## Device Tree Files  

### Kernel Device Tree  
```bash  
kernel/linux-5.4/arch/arm/boot/dts/t113-s3-kickpi-k4b.dts  
# This file selects the active screen device tree. Only one can be chosen.  
# Default: 7-inch LVDS screen. Ensure consistency with U-Boot configuration.  

//#include "t113-s3-kickpi-lcd-tv.dtsi"  
#include "t113-s3-kickpi-lcd-lvds-7-1024-600.dtsi"  
//#include "t113-s3-kickpi-lcd-mipi-10-800-1280.dtsi"  
//#include "t113-s3-kickpi-lcd-mipi-8-800-1280.dtsi"  
```

### U-Boot Device Tree  
```bash  
t113-linux/brandy/brandy-2.0/u-boot-2018/arch/arm/dts/t113-s3-kickpi-k4b-uboot.dts  
# This file selects the active screen device tree. Only one can be chosen.  
# Default: 7-inch LVDS screen. Ensure consistency with kernel configuration.  

#include "t113-s3-kickpi-lcd-lvds-7-1024-600.dtsi"  
//#include "t113-s3-kickpi-lcd-mipi-10-800-1280.dtsi"  
//#include "t113-s3-kickpi-lcd-mipi-8-800-1280.dtsi"  
//#include "t113-s3-kickpi-lcd-tv-out.dtsi"  
```

## LCD Driver Source Code  

* **Kernel**  
```shell  
t113-linux/kernel/linux-5.4/drivers/video/fbdev/sunxi/disp2/disp/lcd  
    panels.c            # 7-inch LVDS LCD driver  
    mipi_8_800x1280.c   # 8-inch MIPI LCD driver  
    mipi_10_800x1280.c  # 10-inch MIPI LCD driver  
```

* **U-Boot**  
```shell  
t113-linux/brandy/brandy-2.0/u-boot-2018/drivers/video/sunxi/disp2/disp/lcd  
    panels.c            # 7-inch LVDS LCD driver  
    mipi_8_800x1280.c   # 8-inch MIPI LCD driver  
    mipi_10_800x1280.c  # 10-inch MIPI LCD driver  
```

## Debugging Information  

**Check current display attributes**:  
```shell  
cat /sys/class/disp/disp/attr/sys  
```
