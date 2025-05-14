# Advanced_Testing

## Hardware Factory Testing

Hardware factory testing is a rapid hardware functionality test based on the DeviceTest application.

It is suitable for test engineers or factory assembly line testing, featuring simple operation and convenient testing.

Steps:

1. Open the calculator in the system application list.

![image-20231021114933026](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021114933026.png)

2. Enter the characters "000.=" in the calculator.

![image-20231021142642942](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021142642942.png)

3. Select "DeviceTest" and click "Just once" or "Always".

![image-20231021181432318](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021181432318.png)

4. The startup interface of the hardware testing program.

![image-20231021140316771](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021140316771.png)

## Hardware Aging Testing

Hardware factory testing is a hardware aging test based on the DeviceTest application.

It mainly targets the aging tests of CPU, DDR, GPU, and VPU.

### Android

**Copy the VPU test video**

The VPU aging test item requires copying the video file to the specified directory of the system or SD card. The aging program will perform a decoding test on the video.

Video file requirements: The video file name and video file path must be consistent with the following.

```
/sdcard/Aging_Test_Video.mp4
```

Two common methods for copying the video:

1. Push the Aging_Test_Video.mp4 video file to the /sdcard directory using the adb push tool.
2. Copy the video file to a USB flash drive or SD card, and then copy it to the /sdcard directory through the file manager.

**Start the aging program**

1. Open the calculator in the system application list.
2. Enter the characters "000.=" in the calculator.
3. Select "DeviceTest" and click "Just once" or "Always".
4. The startup interface of the hardware testing program.

![image-20231021114933026](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021114933026.png)

![image-20231021142650040](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021142650040.png)

![image-20231021140838546](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021140838546.png)

![image-20231021140920296](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021140920296.png)

## Reboot Stress Testing

### **Android**

Steps:

1. Open the calculator in the system application list.
2. Enter the characters "83991906=" in the calculator.

### **Debian**

Start automatic reboot:

```
$ source /rockchip-test/auto_reboot/auto_reboot.sh
```

Check the number of reboots:

```
$ cat /data/rockchip-test/reboot_cnt
```

Turn off automatic reboot:

```
$ echo off > /data/rockchip-test/reboot_cnt
```

Modify the reboot waiting time, for example, to 15 s:

```
$ vi /rockchip-test/auto_reboot/auto_reboot.sh
delay=8
+delay=15
```
