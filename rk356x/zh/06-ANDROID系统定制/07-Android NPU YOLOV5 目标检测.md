# 07-Android NPU YOLOV5 目标检测

测试平台: K1-RK3568-Android13.0

测试demo: rk-android13.0\hardware\rockchip\rknpu2\examples\rknn_yolov5_demo

#### 1、编译demo

环境搭建

安装cmake

```
sudo apt install cmake
//或者使用snapd安装cmake
sudo apt install snapd
sudo snap install cmake --classic
```

下载Android NDK

```
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip android-ndk-r16b-linux-x86_64.zip -d ~/opt
//设置环境变量
export ANDROID_NDK_HOME=~/opt/android-ndk-r16b
export PATH=$ANDROID_NDK_HOME:$PATH
source ~/.bashrc
//验证
$ANDROID_NDK_HOME/ndk-build --version
//验证结果
GNU Make 3.81
Copyright (C) 2006  Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.

This program built for x86_64-pc-linux-gnu
```

编译

```
./build-android_RK3566_RK3568.sh 
```

> 运行对应平台的编译脚本
>
> 生成的所需要的东西均在 install 目录下

### 2.运行demo

将install压缩

```
tar -zcvf  install.tar.gz install/
```

将install.tar.gz adb push 到板子上

```
adb root
adb remount
adb push $PATH/install.tar.gz /data/
```

在板子上运行

进入板子 /data/目录下

```
tar -xvf install.tar.gz
cd install/rknn_yolov5_demo_Android
cp lib/* /vendor/lib/
cp /vendor/lib64/libstdc++.so /vendor/lib
```

运行demo

```
./rknn_yolov5_demo model/RK3566_RK3568/yolov5s-640-640.rknn model/bus.jpg //图片检测
//参数1为模型：model/RK3566_RK3568/yolov5s-640-640.rknn
//参数2为：图片
./rknn_yolov5_video_demo //为视频检测
//三个参数：./rknn_yolov5_video_demo <rknn_model> <video_path> <video_type 264/265>
```

### 3.demo运行的结果

生成的结果在当前目录下out.jpg

![out](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/out.jpg)

