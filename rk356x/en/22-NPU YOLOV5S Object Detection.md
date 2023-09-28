# 22-NPU YOLOV5S







## Video Stream Parsing

### Hardware Environment

Development Board：RK356X

System：Debian11



### Getting the Source Code

* Program source code with built-in SDK directory

```
$ ls external/rknpu2/examples/rknn_yolov5_video_demo/
	build  build-android_RK356X.sh  build-android_RK3588.sh  build-linux_RK356X.sh  build-linux_RK3588.sh  
	CMakeLists.txt  convert_rknn_demo  include  install  model  README.md  src
```



* Obtain test video files from cloud storage

```
1-SDK Source 软件源码/demo/
	rknn_yolov5_demo_test.mp4
```

> You can download any video of your choice



### Compiling the Source Code

* Specify the cross-compilation toolchain path

```
$ export GCC_COMPILER=SDK目录/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/bin/aarch64-rockchip1031-linux-gnu
```

> The cross-compilation toolchain is located in the built-in SDK directory. Please refer to the above command for the specific path.



* Compile the source code

```
$ cd external/rknpu2/examples/rknn_yolov5_video_demo/
$ ./build-linux_RK356X.sh
```

> After successful compilation, execute the program to generate the directory install/rknn_yolov5_demo_Linux/ .



* Copy the program to the development board

```
$ ls install/rknn_yolov5_demo_Linux/
	lib/              model/            rknn_yolov5_demo
```

> Copying can be done using USB flash drive, network, etc.
>
> If you don't want to compile, you can get the executable program from the cloud storage directory 1-SDK Source Software Code/demo/rknn_yolov5_demo_Linux.tar.gz.



### Running the Program 

* Program execution command

```
$ cd rknn_yolov5_demo_Linux/
$ ./rknn_yolov5_demo model/RK356X/yolov5s-640-640.rknn model/test.mp4
```

> RK3566 can only run at 7 frames per second. 
>
> RK3568 can only run at 16 frames per second. 
>
> You can optimize the program performance as needed.



* Program execution interface

![f8944680e7bd81aeec4cbddf2eab4b0](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f8944680e7bd81aeec4cbddf2eab4b0.jpg)
