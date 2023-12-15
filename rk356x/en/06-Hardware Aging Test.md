# 06-Hardware Aging Test

Hardware Aging Test is a hardware aging test based on the DeviceTest application, primarily focusing on CPU, DDR, GPU, and VPU aging tests.



## Copying VPU Test Videos

The VPU aging test requires copying a video file to a specified directory on the system or SD card. The aging program will then perform decoding tests on the video.

**Video File Requirements**:

The video file name and path must match the following:

```
/sdcard/Aging_Test_Video.mp4
```

Below are two common methods for copying the video:

1. Use the adb push tool to push the Aging_Test_Video.mp4 video file to the /sdcard directory.
2. Copy the video file to a USB flash drive or SD card and use a file manager to copy it to the /sdcard directory.



## Accessing the Hardware Aging Program

Steps:

1. Open the calculator from the system application list.
2. Enter the characters "000.=" in the calculator.
3. Select "DeviceTest" and click on "Just once" or "Always."
4. The hardware testing program launches its interface.

![image-20231021114933026](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021114933026.png)

![image-20231021142650040](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021142650040.png)

![image-20231021140838546](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021140838546.png)

![image-20231021140920296](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021140920296.png)
