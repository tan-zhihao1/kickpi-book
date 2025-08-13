# CAMERA



## 环境

* K7主板版本 >= 2.0。

* 支持摄像头 imx415，4 lane。

* MIPI_CS0、MIPI_CS1、MIPI_CS3 为 22 pin 的 MIPI_CSI_RX 接口。



## Android/Linux dts配置

添加摄像头配置，cam0 ~ cam3 对应原理图的 MIPI-CSI_0 ~ MIPI-CSI_3。

**Android / Linux dtsi 文件路径**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam0.dtsi
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam1.dtsi
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam3.dtsi
```

> rk3576-kickpi-k7-cam0.dtsi 对应 MIPI-CSI_0
>
> rk3576-kickpi-k7-cam1.dtsi 对应 MIPI-CSI_1
>
> rk3576-kickpi-k7-cam3.dtsi 对应 MIPI-CSI_3

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



## Armbian dts 配置

添加摄像头配置，cam0 ~ cam3 对应原理图的 MIPI-CSI_0 ~ MIPI-CSI_3。

**Armbian dtsi 文件路径**

```
patch/kernel/rk35xx-vendor-6.1/dt/rk3576-kickpi-k7-cam0.dtsi
patch/kernel/rk35xx-vendor-6.1/dt/rk3576-kickpi-k7-cam1.dtsi
patch/kernel/rk35xx-vendor-6.1/dt/rk3576-kickpi-k7-cam3.dtsi
```

> rk3576-kickpi-k7-cam0.dtsi 对应 MIPI-CSI_0
>
> rk3576-kickpi-k7-cam1.dtsi 对应 MIPI-CSI_1
>
> rk3576-kickpi-k7-cam3.dtsi 对应 MIPI-CSI_3

**多摄像头配置**

使用 MIPI-CSI_0 、MIPI-CSI_1、MIPI-CSI_3 接口都接入 imx415，共三个摄像头。

```
--- a/patch/kernel/rk35xx-vendor-6.1/dt/rk3576-kickpi-k7.dts
+++ b/patch/kernel/rk35xx-vendor-6.1/dt/rk3576-kickpi-k7.dts
@@ -20,6 +20,10 @@
 #include "rk3576-rk806.dtsi"
 #include "rk3576-linux.dtsi"
 
+#include "rk3576-kickpi-k7-cam0.dtsi"
+#include "rk3576-kickpi-k7-cam1.dtsi"
+#include "rk3576-kickpi-k7-cam3.dtsi"
```



## 文档参考

Rockchip 官方提供了 Camera 相关文档，包含在源码SDK中。

Android 源码文档路径

```
(Android SDK)/RKDocs/common/camera/
```

Linux 源码文档路径

```
(Linux SDK)/docs/cn/Linux/Camera/
(Linux SDK)/docs/cn/Common/ISP/
```



## Linux 测试

### **查看摄像头节点**

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



### **查看摄像头信息**

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



### **查看摄像头帧率**

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

>1. **`v4l2-ctl -d /dev/video33`**
>   - `v4l2-ctl`：Video4Linux2 控制工具，用于配置和控制 V4L2 兼容的视频设备
>   - `-d /dev/video33`：指定操作的视频设备为 `/dev/video33`
>2. **`--set-fmt-video=width=1920,height=1080,pixelformat='MJPG'`**
>     配置视频输出格式：
>   - `width=1920,height=1080`：设置视频分辨率为 1920x1080（1080P）
>   - `pixelformat='MJPG'`：设置像素格式为 MJPEG（ Motion JPEG，一种压缩格式）
>3. **`--stream-mmap=4`**
>   - 启用内存映射（mmap）方式捕获视频流
>   - `=4` 表示分配 4 个缓冲区用于流捕获（多缓冲区可以减少帧丢失）
>4. **`--set-selection=target=crop,flags=0,top=0,left=0,width=1920,height=1080`**
>     设置视频裁剪区域：
>   - `target=crop`：指定操作对象为裁剪区域
>   - `flags=0`：无特殊标志（默认）
>   - `top=0,left=0,width=1920,height=1080`：设置裁剪区域从左上角 (0,0) 开始，大小为 1920x1080（即使用完整画面，不裁剪）
>5. **`--stream-count=500`**
>     指定捕获的视频帧数为 500 帧，捕获完成后自动停止



## Debian 测试

### 命令行拍照

```
sudo gst-launch-1.0 v4l2src num-buffers=1 device=/dev/video33 ! jpegenc ! filesink location=picture.jpg
```



### **命令行预览摄像头**

1、先查看摄像头节点

2、指定摄像头头节点进行预览

```
gst-launch-1.0 v4l2src device=/dev/video* ! video/x-raw,format=NV12,width=800,height=600,framerate=30/1 ! videoconvert ! autovideosink
```

示例：

预览 /dev/video33 摄像头。

```
root@linaro-alip:/# gst-launch-1.0 v4l2src device=/dev/video33 ! video/x-raw,format=NV12,width=800,height=600,framerate=30/1 ! videoconvert ! autovideosink
Setting pipeline to PAUSED ...-1.0 v4l2src device=/dev/video33 ! video/x-raw,format=NV12,width=800,height=600,framerate=30/1 ! videoconvert ! autovideosink
libEGL warning: DRI2: failed to authenticate
Using mplane plugin for capture 
Pipeline is live and does not need PREROLL ...
Got context from element 'autovideosink0': gst.gl.GLDisplay=context, gst.gl.GLDisplay=(GstGLDisplay)"\(GstGLDisplayX11\)\ gldisplayx11-0";
Pipeline is PREROLLED ...
Setting pipeline to PLAYING ...
New clock: GstSystemClock
Redistribute latency...
0:00:04.1 / 99:99:99.
```

> 1. **`gst-launch-1.0`**
>    GStreamer 的命令行工具，用于快速构建和运行 GStreamer 管道。
> 2. **`v4l2src device=/dev/video33`**
>    - `v4l2src`：视频 4Linux2 源元件，用于从符合 V4L2 标准的视频设备（如摄像头）捕获数据
>    - `device=/dev/video33`：指定要使用的视频设备文件，这里是 `/dev/video33`（不同设备的摄像头编号可能不同）
> 3. **`video/x-raw,format=NV12,width=800,height=600,framerate=30/1`**
>    这是一个**Caps（功能描述）**，用于指定视频格式：
>    - `video/x-raw`：表示原始未压缩视频
>    - `format=NV12`：视频像素格式为 NV12（一种常见的 YUV 格式，适合硬件加速）
>    - `width=800,height=600`：视频分辨率为 800x600
>    - `framerate=30/1`：帧率为 30 帧 / 秒
> 4. **`videoconvert`**
>    视频格式转换元件，用于将输入视频格式转换为后续元件支持的格式（这里主要是为了适配显示元件的需求）
> 5. **`autovideosink`**
>    自动选择合适的视频输出元件，会根据系统环境自动选择最佳的视频显示方式（如 X11、Wayland 或直接渲染等）



## Armbian Ubuntu 测试

### 安装工具

```
sudo apt update
sudo apt install v4l-utils
sudo apt install gstreamer1.0-tools
sudo apt install gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

### 命令行预览摄像头

1、先查看摄像头节点

2、指定摄像头头节点进行预览

```
gst-launch-1.0 v4l2src device=/dev/video33 ! video/x-raw,width=1280,height=720,framerate=30/1 ! videoconvert ! waylandsink
gst-launch-1.0 v4l2src device=/dev/video42 ! video/x-raw,width=1280,height=720,framerate=30/1 ! videoconvert ! waylandsink
```





## Android APP

Camera2 APP 源码路径。

```
(Android SDK)/packages/apps/Camera2/
```



## Android 测试

> 以双路摄像头为例，前置+后置摄像头。

1、打开 Camera APP。

![image-20250813152220642](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250813152220642.png)

2、切换前置和后置摄像头，需要点击3个小点配置。

![image-20250813152323711](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250813152323711.png)

3、点击按钮，切换前置和后置摄像头。

![image-20250813152343947](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250813152343947.png)