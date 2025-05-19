```markdown
# LCD Functional Debugging

## Kernel Device Tree

* **Top-Level Device Tree Directory**  

**Android SDK Path**:  
```bash
longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts

#include "lcd-lvds-7-1024-600.dtsi"
//#include "lcd-lvds-21-1920-1080.dtsi"
//#include "lcd-mipi-10-800-1280-v1.dtsi"
//#include "lcd-mipi-10-800-1280-v2.dtsi"
//#include "lcd-mipi-8-800-1280.dtsi"
//#include "lcd-mipi-5-720-1280.dtsi"
```

**Linux SDK Path**:  
```bash
device/config/chips/a133/configs/c3/kickpi-k5c.dts
```

* **Secondary Device Tree Directory**  

**Android/Linux SDK Path**:  
```bash
longan/kernel/linux-4.9/arch/arm64/boot/dts/sunxi/
```

| Device Tree Files              | Description                  |
| ------------------------------ | ---------------------------- |
| `lcd-lvds-21-1920-1080.dtsi`   | 21-27" LVDS (1920x1080)      |
| `lcd-lvds-7-1024-600.dtsi`     | 7" LVDS (1024x600)           |
| `lcd-mipi-10-800-1280-v1.dtsi` | 10.1" MIPI (800x1280, v1)    |
| `lcd-mipi-10-800-1280-v2.dtsi` | 10.1" MIPI (800x1280, v2)    |
| `lcd-mipi-5-720-1280.dtsi`     | 5" MIPI (720x1280)           |
| `lcd-mipi-8-800-1280.dtsi`     | 8" MIPI (800x1280)           |

---

### Screen Selection and Compilation

**MIPI Screens**  
| **Model**       | **Size**     | **Resolution** | **Device Tree File**              |
| --------------- | ------------ | -------------- | ---------------------------------- |
| AT101DS40I      | 10.1-inch    | 800x1280       | `lcd-mipi-10-800-1280-v1.dtsi`    |
| MX101BA1340     | 10.1-inch    | 800x1280       | `lcd-mipi-10-800-1280-v2.dtsi`    |
| MX080B2140      | 8.0-inch     | 800x1280       | `lcd-mipi-8-800-1280.dtsi`        |
| F050008M01      | 5.0-inch     | 720x1280       | `lcd-mipi-5-720-1280.dtsi`        |

**LVDS Screens**  
| **Model**       | **Size**     | **Resolution** | **Device Tree File**              |
| --------------- | ------------ | -------------- | ---------------------------------- |
| MX070IBC        | 7.0-inch     | 1024x600       | `lcd-lvds-7-1024-600.dtsi`        |
| -               | 21-27 inch   | 1920x1080      | `lcd-lvds-21-1920-1080.dtsi`      |

**Example**: Selecting the 7" LVDS screen:  
```shell
$ vim longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
#include "lcd-lvds-7-1024-600.dtsi"  // Enable 7" LVDS
//#include "lcd-lvds-21-1920-1080.dtsi"  // Disable others
//#include "lcd-mipi-10-800-1280.dtsi"
```

> **Note**: Only **one** `lcd-*.dtsi` file can be enabled at a time.

---

## LCD Driver

* **UBOOT Driver Path**:  
```bash
longan/brandy/brandy-2.0/u-boot-2018/drivers/video/sunxi/disp2/disp/lcd/
    mipi_800x1280.c       // MIPI LCD driver
```

* **Kernel Driver Path**:  
```bash
longan/kernel/linux-4.9/drivers/video/fbdev/sunxi/disp2/disp/lcd/
    mipi_800x1280.c       // MIPI LCD driver
```

---

### Single LVDS Interface

- **Hardware Label**: `LVDS_LCD` (30-pin clamp connector).  
- **Default Configuration**: Supports 7" LVDS screen (1024x600).  

---

### Dual LVDS Interface

- **Hardware Label**: `DUAL_LVDS` (30-pin vertical connector).  
- **Voltage Options**: 3.3V, 5V, 12V. **Select voltage based on screen specifications to avoid damage!**  

**K5 Board**:  
![image-20250312140810001](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250312140810001.png)  

**K5C Board**:  
![image-20250508150845033](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250508150845033.png)  

> **Warnings**:  
> 1. Ensure the power adapter provides sufficient current for the screen. Insufficient current may cause backlight flickering.  
> 2. If flickering occurs, **disconnect power immediately** and replace the adapter.  

---

## Debugging Information

**View Current Display Properties**:  
```shell
cat /sys/class/disp/disp/attr/sys 
```

**Example Output**:  
```shell
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