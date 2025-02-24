# 14 - Hardware Aging Testing

Hardware factory testing is a hardware aging test based on the DeviceTest application

Mainly for CPU, DDR, GPU, VPU aging test



## Hardware aging program

> Android Platform

### Copy the VPU test video

VPU aging test item, you need to copy the video file to the designated directory of the system or SD card, and the aging program will enter the decoding test for the video.

** Video file requirements **:

The video file name and video file path must match the following

```
/sdcard/Aging_Test_Video.mp4
```

There are two common ways to copy videos:

1. Push the Aging_Test_Video .mp4 video file to the/sdcard directory through the adb push tool
2. Copy the video file to a USB stick or SD card, through the file manager, to the/sdcard directory



### Open the aging program

1. Open the calculator in the system application list
2. Calculator Enter the character "000. ="
3. Select "DeviceTest" and click Just once or Always
4. Hardware test program startup interface



![image-20231021114933026](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021114933026.png)





![image-20231021142650040](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021142650040.png)





![image-20231021140838546](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021140838546.png)







