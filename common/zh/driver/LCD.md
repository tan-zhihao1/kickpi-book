# LCD

主板显示接口

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





## 文档参考

Android_SDK

```
RKDocs/common/display/
```

Linux_SDK

```
docs/cn/Common/DISPLAY/
docs/en/Common/DISPLAY/
```


