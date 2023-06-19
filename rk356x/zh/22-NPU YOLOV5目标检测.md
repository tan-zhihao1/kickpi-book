# 22-NPU YOLOV5S目标检测





## 获取源码

* 程序源码内置SDK目录

```
$ ls external/rknpu2/examples/rknn_yolov5_video_demo/
	build  build-android_RK356X.sh  build-android_RK3588.sh  build-linux_RK356X.sh  build-linux_RK3588.sh  
	CMakeLists.txt  convert_rknn_demo  include  install  model  README.md  src
```



## 编译源码

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
```





## 程序运行

### RK356X Debian11解析视频流

* 程序运行命令

```
$ cd rknn_yolov5_demo_Linux/
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```

> RK356X只能运行到7帧/秒，可自行优化程序性能



* 程序运行界面

![f8944680e7bd81aeec4cbddf2eab4b0](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f8944680e7bd81aeec4cbddf2eab4b0.jpg)
