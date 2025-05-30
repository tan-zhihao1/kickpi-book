# 03-Linux_NPU_YOLOv5s_Object_Detection

## Symbol Explanation

* `SDK$`: Refers to the source code path.

* `console$`: Generally refers to the command-line console of the motherboard. [Mainboard Command-line Console](..\02-Getting_Started_Guide\02-Quick_Start_Guide.md#console_readme)

* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

## 1. What is NPU YOLOV5S

YOLOV5S is an object detection algorithm in computer vision. It is part of the YOLO (You Only Look Once) series, developed by Joseph Redmon, Alexey Bochkovskiy, and others. The YOLO algorithm series is known for its fast real-time object detection capabilities, which can detect and locate multiple objects in images or videos, including the object's category and position.

YOLOv5S is a variant in the YOLOv5 series. Different versions of YOLOv5 have different performance and model complexities. Usually, the "S" in YOLOv5S may stand for "Small", which means it might be a lightweight model suitable for running in resource-constrained environments, such as mobile devices or embedded systems. YOLOv5S may make a trade-off between speed and accuracy to adapt to different application scenarios.

## 2. Image and Video Parsing

RK Linux officially provides the rknn_yolov5_demo. The source code supports `RK3576`. You can download it from the network disk or compile the corresponding tools for image and video parsing.

### Network Disk Path

```
3-SoftwareData/Linux_rknn_yolov5/rknn_yolov5_demo_Linux_rk3576.zip
3-SoftwareData/Linux_rknn_yolov5/yolov5_test.h264
```

### Source Code Path

```
(SDK)/external/rknpu2/examples/rknn_yolov5_demo
```

### Source Code Reference and Compilation

Configure the environment.

The toolchain path is `(SDk)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/`.

```
(SDK)$ export TOOL_CHAIN=(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
(SDK)$ export GCC_COMPILER=(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu
```

> SDK refers to the Linux source code path. For details, see 11-SDK Source Code Compilation.
>
> For example:
>
> rk3576-linux$ pwd
>
> /home/test/rk3576-linux  (SDK Linux source code path)

Compile the corresponding tools.

```
(SDK)$ cd external/rknpu2/examples/rknn_yolov5_demo/
rknn_yolov5_demo$ ./build-linux.sh -t rk3576 -a aarch64 -b Release
```

The path of the generated tools.

```
rknn_yolov5_demo$ ls install/rknn_yolov5_demo_Linux/
lib  model  rknn_yolov5_demo  rknn_yolov5_video_demo
```

> Copy rknn_yolov5_demo_Linux/ to the motherboard for use.
>
> You need to add execution permissions when running.
>
> ​		chmod +x rknn_yolov5_demo
>
> ​		chmod +x rknn_yolov5_video_demo

### Tool Usage

#### rknn_yolov5_demo

```
Usage: ./rknn_yolov5_demo <rknn model> <input_image_path> <resize/letterbox> <output_image_path>
```

Example of using rknn_yolov5_demo.

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

> person @ / bus @ are the corresponding recognition information.

The recognized image out.jpg is generated.

```
(console)$ ls *.jpg
out.jpg
```

![yolov5_out](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/yolov5_out.jpg)

#### rknn_yolov5_video_demo

```
Usage: ./rknn_yolov5_video_demo <rknn_model> <video_path> <video_type 264/265>
```

> Note that you need to use a video with h264/h265 codec stream.

Example of using rknn_yolov5_video_demo.

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

> car @ / bus @ are the corresponding video recognition information.

The recognized video out.h264 is generated.

```
(console)$ ls *.h264
out.h264
```

![yolov5_video](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/yolov5_video.png)