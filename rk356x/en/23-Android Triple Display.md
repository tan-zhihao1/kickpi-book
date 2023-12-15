# 23- Android Triple Display



## 1. Display Interface Resources Introduction

RK3568 has three VP nodes, and each VP node can connect to a display. The correspondences are shown in the table below:

| VP Number | Supported Display Interfaces      | Maximum Resolution |
| --------- | --------------------------------- | ------------------ |
| VP0       | MIPI0 / MIPI1 / HDMI / EDP        | 4096x2304@60Hz     |
| VP1       | MIPI0 / MIPI1 / LVDS / HDMI / EDP | 2048x1536@60Hz     |
| VP2       | LVDS / RGB                        | 1920x1080@60Hz     |

> MIPI0 and LVDS interfaces have pin conflicts and can only be selected one at a time.
>
> VP2 node only supports LVDS/RGB, so one of the three screens must be LVDS (RGB interface is not exposed).

| Display Interface | Maximum Resolution | Format           |
| ----------------- | ------------------ | ---------------- |
| HDMI              | 4096x2160@60Hz     | RGB/YUV420 10Bit |
| MIPI              | 1920x1080@60Hz     | RGB 8Bit         |
| Dual MIPI         | 2560x1440@60Hz     | RGB 8Bit         |
| LVDS              | 1280x800@60Hz      | RGB 8Bit         |
| EDP               | 2560x1600@60Hz     | RGB 10Bit        |
| RGB               | 1920x1080@60Hz     | RGB 8Bit         |

## 2. Single Display Driver Configuration

To configure a single display, modify the device tree LCD configuration file. The RK3568 device tree LCD configuration directory is as follows:

```shell
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1a.dtsi

// LCD
//#include "rk3568-kickpi-lcd-hdmi.dtsi"
//#include "rk3568-kickpi-lcd-edp-15.6-1920-1080.dtsi"
//#include "rk3568-kickpi-lcd-mipi0-10.1-800-1280.dtsi"
#include "rk3568-kickpi-lcd-mipi1-10.1-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-lvds-7-1024-600.dtsi"
```

> If there are issues with the display, please contact support for technical assistance.

### MIPI

RK3568 has two MIPI LCD interfaces, MIPI0 and MIPI1. MIPI0 and LVDS share the same pins.

Refer to `rk3568-kickpi-lcd-mipi0-10.1-800-1280.dtsi` for MIPI0 interface.

Refer to `rk3568-kickpi-lcd-mipi1-10.1-800-1280.dtsi` for MIPI1 interface.

### LVDS

Refer to `rk3568-kickpi-lcd-lvds-7-1024-600.dtsi` for LVDS interface.

### EDP

Refer to `rk3568-kickpi-lcd-edp-15.6-1920-1080.dtsi` for EDP interface.

## 3. Multi-Screen Display System Configuration

In a multi-screen display setup, note the following when selecting LCD parameters:

It is recommended to choose displays with consistent aspect ratios; otherwise, there may be issues such as stretching or black borders.

### Kernel Device Tree Configuration

```shell
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1a.dtsi

// Triple LCD
//#include "rk3568-kickpi-triple-lcd-hdmi-mipi1-lvds.dtsi"
//#include "rk3568-kickpi-triple-lcd-edp-mipi1-lvds.dtsi"
```

> After completing the single-screen debugging, refer to the provided multi-screen device tree configuration files above.

### Android Multi-Screen Configuration

* DSI as the primary screen, HDMI-A as the extended screen:

```shell
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=HDMI-A
```

* DSI\eDP as the primary screen, LVDS\HDMI-A as the extended screen:

```shell
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI,eDP
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=LVDS,HDMI-A
```

## 4. Single Touch Screen Driver Configuration

...

## 5. Multi-Touch Screen System Configuration

### Kernel Device Tree Configuration

I2C + I2C

### Android Single-Touch Configuration (Android 13.0)

Disable touch on the extended screen; regardless of the external or internal device type, touch will operate on the primary screen.

```shell
$ vim frameworks/native/services/inputflinger/reader/EventHub.cpp

// Determine whether the device is external or internal.
if (isExternalDeviceLocked(device)) {
-   device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
+   //device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
}
```

### Android Multi-Touch Configuration

* Identify the device name:

```shell
$ vim frameworks/native/services/inputflinger/reader/EventHub.cpp

bool EventHub::isExternalDeviceLocked(Device* device) {
+    const char *I2C_DEVICE_NAME = "generic ft5x06 (79)";
+    ALOGE("input device is '%s' ", device->identifier.name.c_str());
+    if (strcmp(device->identifier.name.c_str(), I2C_DEVICE_NAME) == 0) {
+        return true;
+    }

    if (device->configuration) {
    bool value;
}
```

* Add IDC configuration files:

```shell

```

### Linux Multi-Touch Configuration

...
