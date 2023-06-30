# 22-NPU YOLOV5S







## 视频流解析

### 硬件环境

开发板：RK356X

系统：Debian11



### 获取源码

* 程序源码内置SDK目录

```
$ ls external/rknpu2/examples/rknn_yolov5_video_demo/
	build  build-android_RK356X.sh  build-android_RK3588.sh  build-linux_RK356X.sh  build-linux_RK3588.sh  
	CMakeLists.txt  convert_rknn_demo  include  install  model  README.md  src
```



* 网盘获取测试视频文件

```
1-SDK Source 软件源码/demo/
	rknn_yolov5_demo_test.mp4
```

> 可自行下载任意视频



### 编译源码

* 指定交叉编译工具链路径

```
$ export GCC_COMPILER=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-rockchip1031-linux-gnu
```

> 交叉编译工具链内置SDK目录，具体路径参考以上命令



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
