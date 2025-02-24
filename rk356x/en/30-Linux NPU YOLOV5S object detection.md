# 30- Linux NPU YOLOV5S object detection



## 1.What is NPU YOLOV5S?

YOLOV5S is an object detection algorithm in computer vision. It is part of the YOLO (You Only Look Once) series, developed by Joseph Redmon and Alexey Bochkovskiy, among others. The YOLO family of algorithms is known for its fast real-time object detection capabilities, capable of detecting and locating multiple objects in an image or video, including the object's category and location.

YOLOv5S is a variant in the YOLOv5 family. Different versions of YOLOv5 have different performance and model complexity. In general, "S" may stand for "Small" in YOLOv5S, which means it may be a lightweight model suitable for operating in resource-constrained environments, such as mobile devices or embedded systems. YOLOv5S may trade off between speed and accuracy to suit different application scenarios.



## 2. Analyze images and videos

RK Linux official rknn_yolov5_demo, source code support'RK3562 ',' RK3566 ',' RK3568 ',' RK3588 ', compile tools for image analysis and video analysis

### source code path

```
(SDK)/external/rknpu2/examples/rknn_yolov5_demo
```



### Source code reference and compilation

configuration environment

```
$ export TOOL_CHAIN=(SDK PATH)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
$ export GCC_COMPILER=(SDK PATH)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu
```

> SDK directory refers to, in fact, it should be replaced with its own actual directory

Compile the corresponding tool

```
$ cd external/rknpu2/examples/rknn_yolov5_demo/
$ ./build-linux_RK3562.sh
```

>build-android_RK3562.sh
>build-android_RK3566_RK3568.sh
>build-android_RK3588.sh
>build-linux_RK3562.sh
>build-linux_RK3566_RK3568.sh
>build-linux_RK3588.sh
>
>Select the corresponding script according to the actual IC for compilation

build tool path

```
rknn_yolov5_demo$ ls install/rknn_yolov5_demo_Linux/
lib  model  rknn_yolov5_demo  rknn_yolov5_video_demo
```

> Copy to the main board to run



### tool use

#### rknn_yolov5_demo

```
Usage: ./rknn_yolov5_demo <rknn model> <jpg>
```

Examples rknn_yolov5_demo use

```
$ ./rknn_yolov5_demo model/RK3588/yolov5s-640-640.rknn model/bus.jpg
post process config: box_conf_threshold = 0.25, nms_threshold = 0.45
Read model/bus.jpg ...
img width = 640, img height = 640
Loading mode...
sdk version: 1.5.2 (c6b7b351a@2023-08-23T15:28:22) driver version: 0.9.2
model input num: 1, output num: 3
  index=0, name=images, n_dims=4, dims=[1, 640, 640, 3], n_elems=1228800, size=1228800, w_stride = 640, size_with_stride=1228800, fmt=NHWC, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=0, name=output, n_dims=4, dims=[1, 255, 80, 80], n_elems=1632000, size=1632000, w_stride = 0, size_with_stride=1638400, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003860
  index=1, name=283, n_dims=4, dims=[1, 255, 40, 40], n_elems=408000, size=408000, w_stride = 0, size_with_stride=491520, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003922
  index=2, name=285, n_dims=4, dims=[1, 255, 20, 20], n_elems=102000, size=102000, w_stride = 0, size_with_stride=163840, fmt=NCHW, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003915
model is NHWC input fmt
model input height=640, width=640, channel=3
once run use 31.119000 ms
loadLabelName ./model/coco_80_labels_list.txt
person @ (209 244 286 506) 0.884139
person @ (478 238 559 526) 0.867678
person @ (110 238 230 534) 0.824685
bus @ (94 129 553 468) 0.705055
person @ (79 354 122 516) 0.339254
loop count = 10 , average run  23.615900 ms
```

> person @/bus @is the corresponding identification information



#### rknn_yolov5_video_demo

```
Usage: ./rknn_yolov5_video_demo <rknn_model> <video_path> <video_type 264/265>
```

> Note that h264/h265 code streaming video is required

Examples rknn_yolov5_video_demo use

```
$ ./rknn_yolov5_video_demo model/RK3588/yolov5s-640-640.rknn model/test.h264 264
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

> car @/bus @is the corresponding video identification information



## 3. Parse local video streams

### Hardware environment

Testing environment: RK3568 Debian11

> Currently RK3568, RK3588 can use this demo



### Sample program testing

YOLOV5S object detection sample program, built into the Debian11 file system

Go to the sample program directory

```
$ cd /rockchip-test/npu2/rknn_yolov5_demo_Linux/
```

Run the sample program

```
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```



### Get the sample program source code

* Program source code built-in SDK directory

```
$ ls external/rknpu2/examples/rknn_yolov5_video_demo/
	build  build-android_RK356X.sh  build-android_RK3588.sh  build-linux_RK356X.sh  build-linux_RK3588.sh  
	CMakeLists.txt  convert_rknn_demo  include  install  model  README.md  src
```



### Compile the source code

* Specify the cross-compilation toolchain path

```
$ export TOOL_CHAIN=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
$ export GCC_COMPILER=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu
```

> Cross-compilation toolchain built-in SDK directory, the specific path can be modified by referring to the above command



* Compile source code

```
$ cd external/rknpu2/examples/rknn_yolov5_video_demo/
$ ./build-linux_RK356X.sh
```

> After the compilation is successful, execute the program to generate the directory install/rknn_yolov5_demo_Linux/



* Copy the program to the development board

```
$ ls install/rknn_yolov5_demo_Linux/
	lib/              model/            rknn_yolov5_demo
```

> Currently rknn_yolov5_demo_Linux /lib need to use the main board /rockchip-test/npu2/rknn_yolov5_demo_Linux/lib
>
> Copy method can be used with U disk, network, etc
>
> If you don't want to compile, you can get the executable program from the network disk directory 1-SDK Source software source code/demo/rknn_yolov5_demo_Linux .tar.gz

Run example:

```
$ cd rknn_yolov5_demo_Linux/
$ export LD_LIBRARY_PATH=/usr/local/opencv4/lib/
$ export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/opencv4/lib/
$ export PKG_CONFIG_LIBDIR=$PKG_CONFIG_LIBDIR:/usr/local/opencv4/lib/pkgconfig
$ cp /rockchip-test/npu2/rknn_yolov5_demo_Linux/lib . -rf
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```



### program running

* Program run command

```
$ cd rknn_yolov5_demo_Linux/
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```

> RK356X can only run to 7 frames per second, and can optimize the program performance by itself



* Program running interface

![f8944680e7bd81aeec4cbddf2eab4b0](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f8944680e7bd81aeec4cbddf2eab4b0.jpg)



## 4. Parse the camera video stream

