# 40-RK3568 LCD Configuration

RK3568 is equipped with four display output interfaces, namely HDMI, MIPI, LVDS, EDP, of which up to three simultaneous display outputs are supported



## 1. Display interface resource introduction

### RK3568

RK3568 has three VP nodes, and each VP node can be connected to a display screen. The corresponding relationship is shown in the table below

| VP Number | VP Support Display Interface | VP Maximum Resolution |
| ------ | --------------------------------- | -------------- |
| VP0 | MIPI0/MIPI1/HDMI/EDP | 4096x2304@60Hz |
| VP1 | MIPI0/MIPI1/LVDS/HDMI/EDP | 2048x1536@60Hz |
| VP2 | LVDS/RGB | 1920x1080@60Hz |

> MIPI0 conflicts with LVDS interface pins, only one can be selected
>
The VP2 node only supports LVDS/RGB, so one of the three-screen displays must be LVDS (the RGB interface is not introduced).



| Display Interface | Display Maximum Resolution | Format |
| --------- | -------------- | ---------------- |
| HDMI | 4096x2160@60Hz | RGB/YUV420 10Bit |
| MIPI | 1920x1080@60Hz | RGB 8Bit |
| Dual MIPI | 2560x1440@60Hz | RGB 8Bit |
| LVDS | 1280x800@60Hz | RGB 8Bit |
| EDP | 2560x1600@60Hz | RGB 10Bit |
| RGB | 1920x1080@60Hz | RGB 8Bit |



## 2. Single display driver configuration

Display configuration, mainly modify the device tree LCD configuration file, RK3568 device tree LCD configuration directory is as follows:

```
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi //linux
$ vim kernel-5.10/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi //android

// LCD
//#include "rk3568-kickpi-lcd-hdmi.dtsi"
//#include "rk3568-kickpi-lcd-edp-15.6-1920-1080.dtsi"
//#include "rk3568-kickpi-lcd-mipi0-10.1-800-1280.dtsi"
#include "rk3568-kickpi-lcd-mipi1-10.1-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-lvds-7-1024-600.dtsi"
```

> If there is a problem with the debugging of the display screen, you can contact the after-sales service for technical support.



### MIPI

The RK3568 has two MIPI LCDs, MIPI0 and MIPI1, of which MIPI0 is multiplexed with LVDS

MIPI0 interface, reference rk3568-kickpi-lcd-mipi0- 10.1-800 -1280.dtsi

MIPI1 interface, reference rk3568-kickpi-lcd-mipi1- 10.1-800 -1280.dtsi



### LVDS

LVDS interface, refer to rk3568-kickpi-lcd-lvds-7-1024-600



### EDP

EDP interface, reference rk3568-kickpi-lcd-edp- 15.6-1920 -1080.dtsi





## 3. Multi-screen display system configuration

In the multi-screen display scheme, LCD parameter selection precautions:

It is recommended to choose a display screen with the same length-width ratio, otherwise the picture will be stretched or the picture will be black.



### Kernel Device Tree Configuration

```
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi

// Triple LCD
//#include "rk3568-kickpi-triple-lcd-hdmi-mipi1-lvds.dtsi"
//#include "rk3568-kickpi-triple-lcd-edp-mipi1-lvds.dtsi"
```

> After completing the single-screen debugging, refer to the multi-screen device tree configuration file provided above



### Android multi-screen configuration

* DSI as the main screen, HDMI-A as the secondary screen

```
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=HDMI-A
```

* DSI\ eDP as the main screen, LVDS\ HDMI-A as the secondary screen

```
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI,eDP
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=LVDS,HDMI-A
```



## 4. Single touchscreen driver configuration

> Take the GT9XX as an example

### driver porting

Porting the driver provided by the manufacturer into the SDK directory, and modifying and adding it to the compiled file

The driver file is copied to the touch driver folder under $ (SDK_DIR) \ kernel-5.10\ drivers\ input\ touchscreen

Modify kconfig: add the following

```
config TOUCHSCREEN_GT9XX
	tristate "GT9XX touchscreens support"
```

Modify the Makefile: Add the following

```
obj-$(CONFIG_TOUCHSCREEN_GT9XX)		+= gt9xx/
```

Kernel Configuration Add CONFIG_TOUCHSCREEN_GT9XX = y

Kernel configuration files such as arch/arm64/configs/rockchip_defconfig, different versions of defconfig please find the corresponding version file in the git history



Kernel Device Tree Configuration

i2c_gt9xx configuration:

```
&i2c1 {
	status = "okay";
	gt9xx:gt9xx@5d {
		compatible = "goodix,gt9xx";
		status = "okay";
		reg = <0x5d>;

		gtp_resolution_x = <1024>;
		gtp_resolution_y = <600>;
		gtp_int_tarigger = <1>;
		gtp_change_x2y = <0>;
		gtp_overturn_x = <0>;
		gtp_overturn_y = <0>;
		gtp_send_cfg = <1>;
		gtp_touch_wakeup = <1>;
		goodix_rst_gpio = <&gpio0 RK_PB6 GPIO_ACTIVE_HIGH>;
		goodix_irq_gpio = <&gpio3 RK_PA3 IRQ_TYPE_LEVEL_LOW>;
		pinctrl-names = "default";
		pinctrl-0 = <&touch3_gpio>;

		goodix,cfg-group0 = [
			4B 00 04 58 02 01 0D 00 01 08
			28 05 50 32 03 05 00 00 00 00
			00 00 00 00 00 00 00 8B 2A 0C
			17 15 31 0D 00 00 02 BA 03 2D
			00 00 00 00 00 03 00 00 00 00
			00 0F 41 94 C5 02 07 00 00 04
			9B 11 00 77 17 00 5C 1F 00 48
			2A 00 3B 38 00 3B 00 00 00 00
			00 00 00 00 00 00 00 00 00 00
			00 00 00 00 00 00 00 00 00 00
			00 00 00 00 00 00 00 00 00 00
			00 00 02 04 06 08 0A 0C 0E 10
			12 14 16 18 FF FF 00 00 00 00
			00 00 00 00 00 00 00 00 00 00
			00 00 00 02 04 06 08 0A 0C 0F
			10 12 13 16 18 1C 1D 1E 1F 20
			21 22 24 FF FF FF FF FF 00 00
			00 00 00 00 00 00 00 00 00 00
			00 00 00 00 B6 01
		];
	};
};
```



### Rotate the touch direction

Adjust the touch screen orientation based on the device tree. The following directions are only applicable to GT9XX drivers

0 degrees (default)

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <0>;		
	gtp_overturn_x = <0>;
	gtp_overturn_y = <0>;
};
```

90 degrees

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <1>;
	gtp_overturn_x = <1>;
	gtp_overturn_y = <0>;
};
```

180 degrees

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <0>;
	gtp_overturn_x = <0>;
	gtp_overturn_y = <1>;
};
```

270 degrees

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <0>;
	gtp_overturn_x = <1>;
	gtp_overturn_y = <0>;
};
```





## 5. Multi-screen touch system configuration

Kernel Device Tree Configuration

I2C + I2C



### Android single-screen touch configuration

* Android13.0

Prohibit secondary screen touch, regardless of external or internal type of device, it acts on the main screen

```
$ vim frameworks/native/services/inputflinger/reader/EventHub.cpp

    // Determine whether the device is external or internal.
    if (isExternalDeviceLocked(device)) {
-        device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
+        //device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
    }
```



### Android multi-screen touch configuration

* Determine the device name

```
$ vim frameworks/native/services/inputflinger/reader/EventHub.cpp

bool EventHub::isExternalDeviceLocked(Device* device) {
+	const char *I2C_DEVICE_NAME = "generic ft5x06 (79)";
+	ALOGE("input deviceis '%s' ",device->identifier.name.c_str());
+	if (strcmp(device->identifier.name.c_str(), I2C_DEVICE_NAME) == 0) {
+		return true;
+	}

	if (device->configuration) {
	bool value;
}
```



* Add IDC configuration file

```
```















