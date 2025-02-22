# 31-Android NPU YOLOV5 object detection

Test Platform: K1-RK3568-Android13.0

Test demo: rk-android13.0\ hardware\ rockchip\ rknpu2\ examples\ rknn_yolov5_demo

## 1. Compile the demo

environment construction

Install cmake

```
sudo apt install cmake
```
Or use snapd to install cmake.
```
sudo apt install snapd
sudo snap install cmake --classic
```

Download Android NDK

```
wget https://dl.google.com/android/repository/android-ndk-r16b-linux-x86_64.zip
unzip android-ndk-r16b-linux-x86_64.zip -d ~/opt
```
Setting environment variables
```
export ANDROID_NDK_HOME=~/opt/android-ndk-r16b
export PATH=$ANDROID_NDK_HOME:$PATH
source ~/.bashrc
```
verify
```
$ANDROID_NDK_HOME/ndk-build --version
```
validation result
```
GNU Make 3.81
Copyright (C) 2006  Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.

This program built for x86_64-pc-linux-gnu
```

compile

```
./build-android_RK3566_RK3568.sh 
```

> Run the build script for the corresponding platform
>
> Everything you need to generate is in the install directory

###2. Run the demo

Compress the install file

```
tar -zcvf  install.tar.gz install/
```

Push install.tar.gz adb to the board

```
adb root
adb remount
adb push $PATH/install.tar.gz /data/
```

Run on the board

Enter the board /data/directory

```
tar -xvf install.tar.gz
cd install/rknn_yolov5_demo_Android
cp lib/* /vendor/lib/
cp /vendor/lib64/libstdc++.so /vendor/lib
```

### Run demo
image detection

```
./rknn_yolov5_demo model/RK3566_RK3568/yolov5s-640-640.rknn model/bus.jpg 
```
> Parameter 1 for the model: model/RK3566_RK3568/yolov5s-640-640.rknn
> Parameter 2 is: picture

For video detection
```
./rknn_yolov5_video_demo 
```
>Three parameters：./rknn_yolov5_video_demo <rknn_model> <video_path> <video_type 264/265>


## 3.Results of the demo run

The generated result is in the current directory out.jpg

![out](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/out.jpg)

