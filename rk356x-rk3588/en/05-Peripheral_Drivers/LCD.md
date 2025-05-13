# LCD

The RK3568 is equipped with four display output interfaces, namely HDMI, MIPI, LVDS, and EDP. It supports a maximum of three simultaneous display outputs.

## 1. Introduction to Display Interface Resources

### RK3568

The RK3568 has three VP nodes, and each VP node can be connected to a display. The corresponding relationships are shown in the following table:

| VP Number | Supported Display Interfaces | Maximum VP Resolution |
| ------ | --------------------------------- | -------------- |
| VP0    | MIPI0 / MIPI1 / HDMI / EDP        | 4096x2304@60Hz |
| VP1    | MIPI0 / MIPI1 / LVDS / HDMI / EDP | 2048x1536@60Hz |
| VP2    | LVDS / RGB                        | 1920x1080@60Hz |

> The pins of the MIPI0 and LVDS interfaces conflict, so only one can be selected.
>
> The VP2 node only supports LVDS/RGB. Therefore, one of the three displays must be LVDS (the RGB interface is not led out) in a triple-screen display setup.

| Display Interface | Maximum Display Resolution | Format |
| --------- | -------------- | ---------------- |
| HDMI      | 4096x2160@60Hz | RGB/YUV420 10Bit |
| MIPI      | 1920x1080@60Hz | RGB 8Bit         |
| Dual MIPI | 2560x1440@60Hz | RGB 8Bit         |
| LVDS      | 1280x800@60Hz  | RGB 8Bit         |
| EDP       | 2560x1600@60Hz | RGB 10Bit        |
| RGB       | 1920x1080@60Hz | RGB 8Bit         |

## 2. Single Display Driver Configuration <a id='LCDDriver'> </a>

Display configuration mainly involves modifying the device tree LCD configuration file. The device tree LCD configuration directory for the RK3568 is as follows:

```
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi //linux
$ vim kernel-5.10/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi //android

// LCD
#include "rk3568-kickpi-lcd-hdmi.dtsi"
//#include "rk3568-kickpi-lcd-edp-15.6-1920-1080.dtsi"
//#include "rk3568-kickpi-lcd-mipi0-10.1-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-mipi1-10.1-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-lvds-7-1024-600.dtsi"
```

> The `rk3568-kickpi-lcd-hdmi.dtsi` file needs to be enabled by default.
>
> If there are problems with display debugging, you can contact after-sales for technical support.

### MIPI

The RK3568 has two MIPI LCD interfaces, namely MIPI0 and MIPI1. Among them, MIPI0 is multiplexed with LVDS.

For the MIPI0 interface, refer to `rk3568-kickpi-lcd-mipi0-10.1-800-1280.dtsi`.
For the MIPI1 interface, refer to `rk3568-kickpi-lcd-mipi1-10.1-800-1280.dtsi`.

### LVDS

For the LVDS interface, refer to `rk3568-kickpi-lcd-lvds-7-1024-600.dtsi`.

### EDP

For the EDP interface, refer to `rk3568-kickpi-lcd-edp-15.6-1920-1080.dtsi`.

## 3. Multi-Screen Display System Configuration

In a multi-screen display solution, pay attention to the following when selecting LCD parameters:

It is recommended to choose displays with the same aspect ratio; otherwise, the picture may be stretched or have black borders.

### Kernel Device Tree Configuration

```
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi

// Triple LCD
//#include "rk3568-kickpi-triple-lcd-hdmi-mipi1-lvds.dtsi"
//#include "rk3568-kickpi-triple-lcd-edp-mipi1-lvds.dtsi"
```

> On the basis of completing single-screen debugging, refer to the above-provided multi-screen device tree configuration files.

### Android Multi-Screen Configuration

* Use DSI as the primary screen and HDMI-A as the secondary screen

```
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=HDMI-A
```

* Use DSI/eDP as the primary screen and LVDS/HDMI-A as the secondary screen

```
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI,eDP
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=LVDS,HDMI-A
```

## 4. Single Touchscreen Driver Configuration <a id='TouchDriver'> </a>

> Take GT9XX as an example.

### Driver Porting

Port the driver provided by the manufacturer to the SDK directory and modify the compilation files.

Copy the driver files to the touch driver folder `$(SDK_DIR)\kernel-5.10\drivers\input\touchscreen`.

Modify the `kconfig` file by adding the following:

```
config TOUCHSCREEN_GT9XX
	tristate "GT9XX touchscreens support"
```

Modify the `Makefile` by adding the following:

```
obj-$(CONFIG_TOUCHSCREEN_GT9XX)		+= gt9xx/
```

Add `CONFIG_TOUCHSCREEN_GT9XX=y` to the kernel configuration.

The kernel configuration file is, for example, `arch/arm64/configs/rockchip_defconfig`. For different versions of `defconfig`, please find the corresponding version file in the Git history.

### Kernel Device Tree Configuration

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

### Rotating the Touch Direction

Adjust the touchscreen direction based on the device tree. The following directions are only applicable to the GT9XX driver.

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

## 5. Multi-Screen Touch System Configuration

### Kernel Device Tree Configuration

I2C + I2C

### Android Single-Screen Touch Configuration

* Android 13.0

Disable touch on the secondary screen. Whether it is an external or internal device, the touch function only works on the primary screen.

```
$ vim frameworks/native/services/inputflinger/reader/EventHub.cpp

    // Determine whether the device is external or internal.
    if (isExternalDeviceLocked(device)) {
-        device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
+        //device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
    }
```

### Android Multi-Screen Touch Configuration

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

* Add an IDC configuration file

```
```