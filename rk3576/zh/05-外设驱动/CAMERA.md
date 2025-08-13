# CAMERA

## 环境

* K7主板版本 >= 2.0。

* 支持摄像头 imx415，4 lane。

* MIPI_CS0、MIPI_CS1、MIPI_CS3 为 22 pin 的 MIPI_CSI_RX 接口。



## dts配置

添加摄像头配置，cam0 ~ cam3 对应原理图的 MIPI-CSI_0 ~ MIPI-CSI_3。

> kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam0.dtsi 对应 MIPI-CSI_0
>
> kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam1.dtsi 对应 MIPI-CSI_1
>
> kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam3.dtsi 对应 MIPI-CSI_3

**单摄像头配置**

使用 MIPI-CSI_0 接口接入 imx415，仅单个摄像头。

```diff
(SDK)$ vim kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7.dtsi
+#include "rk3576-kickpi-k7-cam0.dtsi"
+//#include "rk3576-kickpi-k7-cam1.dtsi"
+//#include "rk3576-kickpi-k7-cam3.dtsi"
```

**多摄像头配置**

使用 MIPI-CSI_0 、MIPI-CSI_1、MIPI-CSI_3 接口都接入 imx415，共三个摄像头。

```diff
(SDK)$ vim kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7.dtsi
+#include "rk3576-kickpi-k7-cam0.dtsi"
+#include "rk3576-kickpi-k7-cam1.dtsi"
+#include "rk3576-kickpi-k7-cam3.dtsi"
```



## Linux 测试

查看摄像头节点

```
root@linaro-alip:/# v4l2-ctl --list-devices
rkisp-statistics (platform: rkisp):
        /dev/video39
        /dev/video40
        /dev/video48
        /dev/video49

rkcif (platform:rkcif-mipi-lvds):
        /dev/video0
        /dev/video1
        /dev/video2
        /dev/video3
        /dev/video4
        /dev/video5
        /dev/video6
        /dev/video7
        /dev/video8
        /dev/video9
        /dev/video10
        /dev/media0

rkcif (platform:rkcif-mipi-lvds1):
        /dev/video11
        /dev/video12
        /dev/video13
        /dev/video14
        /dev/video15
        /dev/video16
        /dev/video17
        /dev/video18
        /dev/video19
        /dev/video20
        /dev/video21
        /dev/media1

rkcif-mipi-lvds3 (platform:rkcif-mipi-lvds3):
        /dev/media2

rkisp_mainpath (platform:rkisp-vir0):
        /dev/video33
        /dev/video34
        /dev/video35
        /dev/video36
        /dev/video37
        /dev/video38
        /dev/video41
        /dev/media3

rkisp_mainpath (platform:rkisp-vir1):
        /dev/video42
        /dev/video43
        /dev/video44
        /dev/video45
        /dev/video46
        /dev/video47
        /dev/video50
        /dev/media4

rkvpss_scale0 (platform:rkvpss-vir0):
        /dev/video52
        /dev/video53
        /dev/video54
        /dev/video55
        /dev/media5

rkvpss_scale0 (platform:rkvpss-vir1):
        /dev/video56
        /dev/video57
        /dev/video58
        /dev/video59
        /dev/media6

rkvpss_scale0 (platform:rkvpss-vir3):
        /dev/video60
        /dev/video61
        /dev/video62
        /dev/video63
        /dev/media7

root@linaro-alip:/# 
```

