# LCD

## 主板显示接口

| 主板 | 显示输出接口                           | 多路同显 |
| ---- | -------------------------------------- | -------- |
| K1   | HDMI、MIPI DSI、Signal LVDS、EDP       |          |
| K1B  | HDMI、MIPI DSI、Signal LVDS            |          |
| K3   | HDMI、MIPI DSI、Signal LVDS、Dual LVDS |          |
| K7   | HDMI、USB Type-C DP、MIPI DSI          |          |
| K7C  | HDMI、MIPI DSI                         |          |
| K8   | HDMI X2、USB Type-C DP、MIPI DSI       |          |



## 显示接口资源介绍

### RK3568

RK3568 有三个VP节点，每个VP节点可挂接一个显示屏，对应关系如下表格示

| VP编号 | VP支持显示接口                    | VP最大分辨率   |
| ------ | --------------------------------- | -------------- |
| VP0    | MIPI0 / MIPI1 / HDMI / EDP        | 4096x2304@60Hz |
| VP1    | MIPI0 / MIPI1 / LVDS / HDMI / EDP | 2048x1536@60Hz |
| VP2    | LVDS / RGB                        | 1920x1080@60Hz |

> MIPI0与LVDS接口引脚冲突，只能二选一
>
> VP2节点仅支持LVDS/RGB，所以三屏显示时其中一路必须是LVDS（RGB接口未引出）



| 显示接口  | 显示最大分辨率 | 格式             |
| --------- | -------------- | ---------------- |
| HDMI      | 4096x2160@60Hz | RGB/YUV420 10Bit |
| MIPI      | 1920x1080@60Hz | RGB 8Bit         |
| Dual MIPI | 2560x1440@60Hz | RGB 8Bit         |
| LVDS      | 1280x800@60Hz  | RGB 8Bit         |
| EDP       | 2560x1600@60Hz | RGB 10Bit        |
| RGB       | 1920x1080@60Hz | RGB 8Bit         |



### RK3576



### RK3588



## 单显示屏驱动配置<a id='LCDDriver'> </a>

### RK3562

### RK3568

显示屏配置 主要修改设备树LCD配置文件，RK3568 设备树LCD配置目录如下：

```
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi //linux
$ vim kernel-5.10/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi //android

// LCD
#include "rk3568-kickpi-lcd-hdmi.dtsi"
//#include "rk3568-kickpi-lcd-edp-15.6-1920-1080.dtsi"
//#include "rk3568-kickpi-lcd-mipi0-10.1-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-mipi1-10.1-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-mipi0-10.1-800-1280-v2.dtsi"
//#include "rk3568-kickpi-lcd-mipi1-10.1-800-1280-v2.dtsi"
//#include "rk3568-kickpi-lcd-lvds0-7-1024-600.dtsi"
//#include "rk3568-kickpi-lcd-lvds1-7-1024-600.dtsi"
//#include "rk3568-kickpi-lcd-mipi0-5-720-1280.dtsi"
//#include "rk3568-kickpi-lcd-mipi1-5-720-1280.dtsi"
// #include "rk3568-kickpi-lcd-mipi0-8-800-1280.dtsi"
//#include "rk3568-kickpi-lcd-mipi1-8-800-1280.dtsi"
```

> 其中的rk3568-kickpi-lcd-hdmi.dtsi需要默认保持使能
>
> 若显示屏调试有问题，可联系售后，获取技术支持

屏幕设备树名称介绍

```
(CPU类型)-kickpi-lcd-(接口)-(英寸)-(宽)-(高).dtsi
```

### RK3576

**MIPI DSI**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-5-720-1280-F050008M01.dtsi
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-8-800-1280-MX080B2140.dtsi
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-10.1-800-1280-MX101BA1340.dtsi
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-10.1-800-1280-AT101DS40I.dtsi
```

**HDMI**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-lcd-hdmi.dtsi
```

**TYPE-C DP**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-lcd-dp.dtsi
```



### RK3588





## 多屏显示系统配置

多屏显示方案中，LCD参数选择注意事项：

推荐选择显示长宽比例一致的显示屏，否则会出现画面拉伸或者画面黑边



### 内核设备树配置

```
$ vim kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi

// Triple LCD
//#include "rk3568-kickpi-triple-lcd-hdmi-mipi1-lvds.dtsi"
//#include "rk3568-kickpi-triple-lcd-edp-mipi1-lvds.dtsi"
```

> 在完成单屏调试的基础上，参考以上提供的多屏设备树配置文件



### Android多屏配置

* DSI作为主屏，HDMI-A作为副屏

```
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=HDMI-A
```

* DSI\eDP作为主屏，LVDS\HDMI-A作为副屏

```
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.primary=DSI,eDP
PRODUCT_PROPERTY_OVERRIDES += vendor.hwc.device.extend=LVDS,HDMI-A
```



## 单触摸屏驱动配置<a id='TouchDriver'> </a>

> 以GT9XX为例

### 驱动移植

将厂商提供的驱动移植进SDK目录，并修改添加进编译文件

驱动文件copy到触摸驱动的文件夹下$(SDK_DIR)\kernel-5.10\drivers\input\touchscreen

修改kconfig：添加如下

```
config TOUCHSCREEN_GT9XX
	tristate "GT9XX touchscreens support"
```

修改Makefile：添加如下

```
obj-$(CONFIG_TOUCHSCREEN_GT9XX)		+= gt9xx/
```

内核配置添加CONFIG_TOUCHSCREEN_GT9XX=y

内核配置文件如arch/arm64/configs/rockchip_defconfig，不同版本的defconfig请自行在git历史记录中查找对应的版本文件



### 内核设备树配置

i2c_gt9xx配置：

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



### 旋转触摸方向

基于设备树调整触摸屏方向，以下方向仅适用于GT9XX驱动

0度（默认）

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <0>;		
	gtp_overturn_x = <0>;
	gtp_overturn_y = <0>;
};
```

90度

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <1>;
	gtp_overturn_x = <1>;
	gtp_overturn_y = <0>;
};
```

180度

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <0>;
	gtp_overturn_x = <0>;
	gtp_overturn_y = <1>;
};
```

270度

```
gt9xx:gt9xx@5d {
	gtp_change_x2y = <0>;
	gtp_overturn_x = <1>;
	gtp_overturn_y = <0>;
};
```





## 多屏触摸系统配置

### 内核设备树配置

I2C + I2C



### Android单屏触摸配置

* Android13.0

禁止副屏触摸，不论external 或 internal类型设备，都作用在主屏

```
$ vim frameworks/native/services/inputflinger/reader/EventHub.cpp

    // Determine whether the device is external or internal.
    if (isExternalDeviceLocked(device)) {
-        device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
+        //device->classes |= INPUT_DEVICE_CLASS_EXTERNAL;
    }
```



### Android多屏触摸配置

* 判断设备名称

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



* 添加IDC配置文件

```

```



## 文档参考

Android_SDK

```
RKDocs/common/display/
RKDocs/android/patches/customized_functions/Android11异显开发说明.zip
RKDocs/android/patches/customized_functions/Dual_Audio_v1.0.zip
```

Linux_SDK

```
docs/cn/Common/DISPLAY/
docs/en/Common/DISPLAY/
```


