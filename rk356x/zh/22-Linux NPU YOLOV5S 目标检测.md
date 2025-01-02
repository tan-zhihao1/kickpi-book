# 22-Linux NPU YOLOV5S 目标检测





## 1. NPU YOLOV5S 是什么

YOLOV5S 是计算机视觉中的一个目标检测算法，它是YOLO（You Only Look Once）系列的一部分，由Joseph Redmon和Alexey Bochkovskiy等人开发。YOLO 算法系列以其快速的实时目标检测能力而闻名，能够在图像或视频中检测和定位多个对象，包括物体的类别和位置。

YOLOv5S 是 YOLOv5 系列中的一个变种，不同版本的 YOLOv5 具有不同的性能和模型复杂度。通常，"S" 在 YOLOv5S 中可能代表 "Small"，这意味着它可能是一种轻量级的模型，适合在资源受限的环境下运行，如移动设备或嵌入式系统。 YOLOv5S 可能在速度和准确性之间进行权衡，以适应不同的应用场景。



## 2. 解析图片及视频流参考

RK Linux官方提供rknn_yolov5_demo，源码支持 RK3562、RK3566_RK3568、RK3588，编译出工具可进行图像解析和视频解析

### 源码路径

```
(SDK)/external/rknpu2/examples/rknn_yolov5_demo
```



### 源码参考及编译

配置环境

```
$ export TOOL_CHAIN=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
$ export GCC_COMPILER=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu
```

编译对应的工具

```
$ cd external/rknpu2/examples/rknn_yolov5_video_demo/
$ ./build-linux_RK3562.sh
```

>build-android_RK3562.sh
>build-android_RK3566_RK3568.sh
>build-android_RK3588.sh
>build-linux_RK3562.sh
>build-linux_RK3566_RK3568.sh
>build-linux_RK3588.sh
>
>根据实际IC选择对应的脚本进行编译



### 工具使用



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







## 3. 解析本地视频流

### 硬件环境

测试环境：RK3568 Debian11

> 目前 RK3568、RK3588 可使用此demo



### 示例程序测试

YOLOV5S目标检测 示例程序 已内置在Debian11文件系统

进入示例程序目录

```
$ cd /rockchip-test/npu2/rknn_yolov5_demo_Linux/
```

运行示例程序

```
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```



### 获取示例程序源码

* 程序源码内置SDK目录

```
$ ls external/rknpu2/examples/rknn_yolov5_video_demo/
	build  build-android_RK356X.sh  build-android_RK3588.sh  build-linux_RK356X.sh  build-linux_RK3588.sh  
	CMakeLists.txt  convert_rknn_demo  include  install  model  README.md  src
```



### 编译源码

* 指定交叉编译工具链路径

```
$ export TOOL_CHAIN=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
$ export GCC_COMPILER=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu
```

> 交叉编译工具链内置SDK目录，具体路径可参考以上命令修改



* 编译源码

```
$ cd external/rknpu2/examples/rknn_yolov5_video_demo/
$ ./build-linux_RK356X.sh
```

> 编译成功后，执行程序生成目录 install/rknn_yolov5_demo_Linux/



* 拷贝程序到开发板

```
$ ls install/rknn_yolov5_demo_Linux/
	lib/              model/            rknn_yolov5_demo
```

> 拷贝方式可用U盘、网络等
>
> 若不想要编译，可从网盘目录获取可执行程序 1-SDK Source 软件源码/demo/rknn_yolov5_demo_Linux.tar.gz



### 程序运行

* 程序运行命令

```
$ cd rknn_yolov5_demo_Linux/
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```

> RK356X只能运行到7帧/秒，可自行优化程序性能



* 程序运行界面

![f8944680e7bd81aeec4cbddf2eab4b0](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f8944680e7bd81aeec4cbddf2eab4b0.jpg)





## 4. 解析摄像头视频流

