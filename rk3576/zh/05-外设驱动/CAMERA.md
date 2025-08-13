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

**查看摄像头节点**

```
v4l2-ctl --list-devices
```

示例：

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

> 通过 rkisp_mainpath 可知有两个摄像头，可操作对应的第一个设备/dev/video33 和 /dev/video42



**查看摄像头信息**

```
v4l2-ctl --list-formats-ext --device=/dev/video*
```

示例

```
root@linaro-alip:/# v4l2-ctl --list-formats-ext --device=/dev/video33
ioctl: VIDIOC_ENUM_FMT
        Type: Video Capture Multiplanar

        [0]: 'UYVY' (UYVY 4:2:2)
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
        [1]: 'NV16' (Y/UV 4:2:2)
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
        [2]: 'NV61' (Y/VU 4:2:2)
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
        [3]: 'NV21' (Y/VU 4:2:0)
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
        [4]: 'NV12' (Y/UV 4:2:0)
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
        [5]: 'NM21' (Y/VU 4:2:0 (N-C))
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
        [6]: 'NM12' (Y/UV 4:2:0 (N-C))
                Size: Stepwise 32x32 - 3840x2160 with step 8/8
root@linaro-alip:/# 
```



**查看摄像头帧率**

```
v4l2-ctl -d /dev/video* \
 --set-fmt-video=width=1920,height=1080,pixelformat='MJPG' \
 --stream-mmap=4 \
 --set-selection=target=crop,flags=0,top=0,left=0,width=1920,height=1080 \
 --stream-count=500
```

示例

```
root@linaro-alip:/# v4l2-ctl -d /dev/video33 \
 --set-fmt-video=width=1920,height=1080,pixelformat='MJPG' \
 --stream-mmap=4 \
 --set-selection=target=crop,flags=0,top=0,left=0,width=1920,height=1080 \
 --stream-count=500
The pixelformat 'MJPG' is invalid
[  425.753116] rkisp_hw 27c00000.isp: set isp clk = 396000000Hz
[  425.779808] rkisp rkisp-vir0: first params buf queue
[  425.780839] rkcif-mipi-lvds: stream[0] start streaming
[  425.783853] rkcif-mipi-lvds: Allocate dummy buffer, size: 0x01028000
[  425.783941] rockchip-mipi-csi2 mipi0-csi2: stream on, src_sd: 000000004c236c38, sd_name:rockchip-csi2-dphy0
[  425.783950] rockchip-mipi-csi2 mipi0-csi2: stream ON
[  425.783977] rockchip-csi2-dphy0: dphy0, data_rate_mbps 892
[  425.784438] rockchip-csi2-dphy csi2-dcphy0: csi2_dphy_s_stream stream on:1, dphy0, ret 0
[  425.784447] imx415 4-0037: s_stream: 1. 3864x2192, hdr: 0, bpp: 10
[  425.951182] mipi0-csi2-hw ERR2:0xf0000 
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<^C
root@linaro-alip:/#
```



