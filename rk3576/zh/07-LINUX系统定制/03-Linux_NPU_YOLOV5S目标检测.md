# 03-Linux NPU YOLOV5S 目标检测



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## RKNN 部署

系统支持部署 rknn-toolkit2，rknn_model_zoo，参考 rockhip 官方文档如下：

https://github.com/airockchip/rknn-toolkit2/tree/master/doc

https://github.com/airockchip/rknn_model_zoo/tree/main/docs



## 1. NPU YOLOV5S 是什么

YOLOV5S 是计算机视觉中的一个目标检测算法，它是YOLO（You Only Look Once）系列的一部分，由Joseph Redmon和Alexey Bochkovskiy等人开发。YOLO 算法系列以其快速的实时目标检测能力而闻名，能够在图像或视频中检测和定位多个对象，包括物体的类别和位置。

YOLOv5S 是 YOLOv5 系列中的一个变种，不同版本的 YOLOv5 具有不同的性能和模型复杂度。通常，"S" 在 YOLOv5S 中可能代表 "Small"，这意味着它可能是一种轻量级的模型，适合在资源受限的环境下运行，如移动设备或嵌入式系统。 YOLOv5S 可能在速度和准确性之间进行权衡，以适应不同的应用场景。



## 2. 解析图片及视频

RK Linux官方提供rknn_yolov5_demo，源码支持 `RK3576`，可以下载网盘或编译对应工具，可进行图像解析和视频解析。



### 网盘路径

```
3-SoftwareData/Linux_rknn_yolov5/rknn_yolov5_demo_Linux_rk3576.zip
3-SoftwareData/Linux_rknn_yolov5/yolov5_test.h264
```



### 源码路径

```
(SDK)/external/rknpu2/examples/rknn_yolov5_demo
```



### 源码参考及编译

配置环境

工具链路径 `(SDk)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/`

```
(SDK)$ export TOOL_CHAIN=(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
(SDK)$ export GCC_COMPILER=(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu
```

> SDK 指代Linux源码路径，详情见 11-SDK源码编译
>
> 例如：
>
> rk3576-linux$ pwd
>
> /home/test/rk3576-linux  (SDK Linux源码路径)

编译对应的工具

```
(SDK)$ cd external/rknpu2/examples/rknn_yolov5_demo/
rknn_yolov5_demo$ ./build-linux.sh -t rk3576 -a aarch64 -b Release
```

生成工具路径

```
rknn_yolov5_demo$ ls install/rknn_yolov5_demo_Linux/
lib  model  rknn_yolov5_demo  rknn_yolov5_video_demo
```

>将 rknn_yolov5_demo_Linux/ 拷贝到主板上运行使用
>
>运行需要添加执行权限
>
>​		chmod +x rknn_yolov5_demo
>
>​		chmod +x rknn_yolov5_video_demo



### 工具使用

#### rknn_yolov5_demo

```
Usage: ./rknn_yolov5_demo <rknn model> <input_image_path> <resize/letterbox> <output_image_path>
```

rknn_yolov5_demo 使用示例

```
(console)$ export LD_LIBRARY_PATH=./lib
(console)$ ./rknn_yolov5_demo model/RK3576/yolov5s-640-640.rknn model/bus.jpg
post process config: box_conf_threshold = 0.25, nms_threshold = 0.45
Loading mode...
sdk version: 2.0.0b0 (35a6907d79@2024-03-24T10:31:14) driver version: 0.9.7
model input num: 1, output num: 3
  index=0, name=images, n_dims=4, dims=[1, 640, 640, 3], n_elems=1228800, size=1228800, w_stride = 640, size_with_stride=1228800, fmt=NHWC, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=0, name=output0, n_dims=4, dims=[1, 255, 80, 80], n_elems=1632000, size=1632000, w_stride = 0, size_with_stride=1638400, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=1, name=286, n_dims=4, dims=[1, 255, 40, 40], n_elems=408000, size=408000, w_stride = 0, size_with_stride=491520, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=2, name=288, n_dims=4, dims=[1, 255, 20, 20], n_elems=102000, size=102000, w_stride = 0, size_with_stride=163840, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
model is NHWC input fmt
model input height=640, width=640, channel=3
Read model/bus.jpg ...
img width = 640, img height = 640
once run use 48.461000 ms
loadLabelName ./model/coco_80_labels_list.txt
person @ (209 243 286 510) 0.879723
person @ (479 238 560 526) 0.870588
person @ (109 237 232 534) 0.828112
bus @ (93 129 553 464) 0.700761
person @ (79 353 122 517) 0.307297
save detect result to ./out.jpg
loop count = 10 , average run  53.093000 ms
```

>person @ / bus @ 为对应识别信息

生成识别图片 out.jpg

```
(console)$ ls *.jpg
out.jpg
```

![yolov5_out](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/yolov5_out.jpg)



#### rknn_yolov5_video_demo

```
Usage: ./rknn_yolov5_video_demo <rknn_model> <video_path> <video_type 264/265>
```

> 注意需要使用 h264/h265 码流视频

rknn_yolov5_video_demo 使用示例

```
(console)$ ./rknn_yolov5_video_demo model/RK3576/yolov5s-640-640.rknn yolov5_test.h264 264
Loading mode...
sdk version: 1.5.2 (c6b7b351a@2023-08-23T15:28:22) driver version: 0.9.2
model input num: 1, output num: 3
  index=0, name=images, n_dims=4, dims=[1, 640, 640, 3], n_elems=1228800, size=1228800, fmt=NHWC, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=0, name=output, n_dims=4, dims=[1, 255, 80, 80], n_elems=1632000, size=1632000, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003860
  index=1, name=283, n_dims=4, dims=[1, 255, 40, 40], n_elems=408000, size=408000, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=2, name=285, n_dims=4, dims=[1, 255, 20, 20], n_elems=102000, size=102000, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003915
model is NHWC input fmt
model input height=640, width=640, channel=3
mpi_dec_test start mpi_dec_test decoder test start mpp_type 7 app_ctx=0x7fc1305278 decoder=0x23ad0100
read video size=720664
receive packet size=8192 receive packet size=8192 receive packet size=8192 receive packet size=8192 receive packet size=8192 receive packet size=8192 receive packet size=8192 decoder require buffer w:h [1280:720] stride [1280:720] buf_size 1843200 pts=0 dts=0 decode_get_frame get info changed found receive packet size=8192 receive packet size=8192 decoder require buffer w:h [1280:720] stride [1280:720] buf_size 1843200 pts=0 dts=0 get one frame 1687102942317 data_vir=0x7f80024000 fd=13 0x24133340 encoder test start w 1280 h 720 type 7
input image 1280x720 stride 1280x720 format=2560
resize with RGA!
rga_api version 1.9.1_[4]
once run use 26.630000 ms
post process config: box_conf_threshold = 0.25, nms_threshold = 0.45
loadLabelName ./model/coco_80_labels_list.txt
car @ (380 412 438 461) 0.868004
car @ (532 362 574 399) 0.866127
car @ (496 328 530 354) 0.736625
car @ (564 311 592 331) 0.729147
car @ (860 433 928 497) 0.695733
car @ (676 306 704 327) 0.642370
car @ (738 402 792 457) 0.611211
car @ (202 441 296 498) 0.610788
bus @ (748 272 806 329) 0.527151
car @ (440 322 474 347) 0.526350
car @ (514 310 540 326) 0.507014
car @ (280 666 444 720) 0.347807
car @ (670 288 696 302) 0.271905
car @ (540 289 566 307) 0.176622
car @ (494 297 516 312) 0.168131
car @ (702 263 716 273) 0.163944
```

>car @ / bus @ 为对应视频识别信息

生成识别视频 out.h264

```
(console)$ ls *.h264
out.h264
```

![yolov5_video](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/yolov5_video.png)

