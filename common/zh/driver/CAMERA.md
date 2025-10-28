# CAMERA



## 环境



| 主板        | 接口                         | 已适配模块 | 规格                   |
| ----------- | ---------------------------- | ---------- | ---------------------- |
| K1          | MIPI_CSI                     | gc5035     | 4 Lane x1 or 2 Lane x2 |
| K1B         | MIPI_CSI                     | gc5035     | 4 Lane x1 or 2 Lane x2 |
| K3          | MIPI_CSI_RX                  | gc5035     | 4 Lane x2 or 2 Lane x4 |
| K7 (>= 2.0) | MIPI_CS0、MIPI_CS1、MIPI_CS3 | imx415     |                        |
| K7C         | MIPI_CS0、MIPI_CS1、MIPI_CS3 | imx415     |                        |



## Android/Linux dts配置

### RK3576

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

### RK3576

> 目前 Armbian ISP 存在问题，不建议使用

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



## v4l2-ctl 测试

### 安装工具

```
sudo apt update
sudo apt install v4l-utils
```

### **查看摄像头节点**<a id="camera-id"></a> 

```
v4l2-ctl --list-devices
```

示例：

通过 rkisp_mainpath 可知有两个摄像头，可操作对应的第一个设备/dev/video33 和 /dev/video42

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





### **查看摄像头信息**<a id="camera-info"></a> 

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



### **查看摄像头帧率**<a id="camera-fps"></a> 

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
 --set-fmt-video=width=3840,height=2160\
 --stream-mmap=4 \
 --set-selection=target=crop,flags=0,top=0,left=0,width=3840,height=2160 \
 --stream-count=500
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< 30.00 fps
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



## gstreamer 测试

### 安装工具

```
sudo apt update
sudo apt install gstreamer1.0-tools
sudo apt install gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```



### 命令行摄像头捕获图片

```
sudo gst-launch-1.0 v4l2src num-buffers=1 device=/dev/video* ! jpegenc ! filesink location=picture.jpg
```



### **命令行摄像头预览**

> 由于 ISP 问题，Armbian系统目前摄像头预览异常。

autovideosink 方式

```
gst-launch-1.0 v4l2src device=/dev/video* ! video/x-raw,format=NV12,width=800,height=600,framerate=30/1 ! videoconvert ! autovideosink
```

> 例如：
>
> gst-launch-1.0 v4l2src device=/dev/video11 ! video/x-raw,format=NV12,width=800,height=600,framerate=30/1 ! videoconvert ! autovideosink
>
> gst-launch-1.0 v4l2src device=/dev/video33 ! video/x-raw,format=NV12,width=800,height=600,framerate=30/1 ! videoconvert ! autovideosink
>
> gst-launch-1.0 v4l2src device=/dev/video42 ! video/x-raw,format=NV12,width=1280,height=720,framerate=30/1 ! videoconvert ! autovideosink
>
> gst-launch-1.0 v4l2src device=/dev/video51 ! video/x-raw,format=NV12,width=1600,height=960,framerate=30/1 ! videoconvert ! autovideosink

waylandsink 方式（gome wayland 架构桌面使用该方式进行预览）

```
gst-launch-1.0 v4l2src device=/dev/video* ! video/x-raw,width=1280,height=720,framerate=30/1 ! videoconvert ! waylandsink
```

> 例如：
>
> gst-launch-1.0 v4l2src device=/dev/video33 ! video/x-raw,width=800,height=600,framerate=30/1 ! videoconvert ! waylandsink
>
> gst-launch-1.0 v4l2src device=/dev/video42 ! video/x-raw,width=1280,height=720,framerate=30/1 ! videoconvert ! waylandsink
>
> gst-launch-1.0 v4l2src device=/dev/video51 ! video/x-raw,width=1600,height=960,framerate=30/1 ! videoconvert ! waylandsink

示例：

1、先查看摄像头规格；[查看摄像头](#camera-id)、[查看摄像头信息](#camera-info)、[查看摄像头帧率](#camera-fps)

2、指定摄像头头节点进行预览；

3、使用 gst-launch-1.0 工具 autovideosink 方式预览 /dev/video33 摄像头。

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



## Linux Camera

### rkaiq 源码路径

```
external/camera_engine_rkaiq/
```

### 编译 camera-engine-rkaiq

第一步，删除 BR2_ARCH_NEEDS_GCC_AT_LEAST_9

```diff
--- a/buildroot/package/gcc/Config.in.host
+++ b/buildroot/package/gcc/Config.in.host
@@ -17,7 +17,6 @@ config BR2_GCC_VERSION_ARC
 
 config BR2_GCC_VERSION_8_X
        bool "gcc 8.x"
-       depends on !BR2_ARCH_NEEDS_GCC_AT_LEAST_9
        select BR2_TOOLCHAIN_GCC_AT_LEAST_8
 
 config BR2_GCC_VERSION_10_X
diff --git a/envsetup.sh b/envsetup.sh
deleted file mode 120000
index a2f3ce43c..000000000
```

第二步，创建低版本的 GCC 和 GLIBC 编译

```
cat buildroot/configs/rockchip_rk3576_glibc2.28_defconfig
#include "../rockchip_rk3576_defconfig"
BR2_GCC_VERSION_8_X=y
BR2_PACKAGE_GLIBC_2_28=y
```

第三步，编译

```
(SDK)$ source buildroot/envsetup.sh rockchip_rk3576_glibc2.28
(SDK)$ cd buildroot
buildroot$ make camera-engine-rkaiq
```

第四步，将生成的 tar 放到主板根目录

```
output/rockchip_rk3576_glibc2.28/build/camera-engine-rkaiq-1.0/camera-engine-rkaiq-1.0.tar
```

第五步，在主板上解压并重启

```
$ tar -xvf camera-engine-rkaiq-1.0.tar
$ reboot
```



## Android APP

Camera2 APP 源码路径。

```
(Android SDK)/packages/apps/Camera2/
```



## Android ISP 适配

参考 imx415 其他 Profiles 配置对应设备 m02 。更多详解文档参考章节。

```diff
--- a/hardware/rockchip/camera/etc/camera/camera3_profiles_rk3576.xml
+++ b/hardware/rockchip/camera/etc/camera/camera3_profiles_rk3576.xml
@@ -1797,6 +1797,268 @@
             <aiq.workingMode value="NORMAL"/> <!-- NORMAL or HDR2 or HDR3 -->
         </Sensor_info_RKISP1>
 
+<!-- ******************PSL specific section end **************************************************************-->
+    </Profiles>
+        <Profiles cameraId="0" name="imx415" moduleId="m02">
.......
+            <sensor.digitalGain value="true"/> <!-- digital gain support on sensor-->
+            <gain.lag value="2"/> <!-- camera3 HAL CPF parameters moved here start-->
+            <exposure.lag value="2"/>
+            <fov value= "54.8" value_v="42.5"/>
+            <statistics.initialSkip value="1"/> <!-- camera3 HAL CPF parameters moved here end-->
+            <frame.initialSkip value="3"/> <!-- should equal actual skipFrames - 2 for driver dropped 2 frames -->
+            <isoAnalogGain1 value="75"/> <!--Pseudo ISO corresponding analog gain value 1.0. -->
+            <cITMaxMargin value="10"/> <!--coarse integration time max margin -->
+            <aiq.workingMode value="NORMAL"/> <!-- NORMAL or HDR2 or HDR3 -->
+        </Sensor_info_RKISP1>
+
+ <!-- ******************PSL specific section end **************************************************************-->
+     </Profiles>
```





## Android 测试

> 以双路摄像头为例，前置+后置摄像头。

1、打开 Camera APP。

![image-20250813152220642](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250813152220642.png)

2、切换前置和后置摄像头，需要点击3个小点配置。

![image-20250813152323711](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250813152323711.png)

3、点击按钮，切换前置和后置摄像头。

![image-20250813152343947](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250813152343947.png)