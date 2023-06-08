# 01-Quick Start Guide



## 1. Introduction

### Cloud Drive Files

Baidu Cloud Drive Link：

OneDrive Cloud Drive Link：



* Directory Structure

```
├── 0-用前必读
│   └── 资料目录说明.md
├── 1-SDK源码
│   └── RK356X Linux
│       ├── linaro-rootfs-20230524.tar.gz
│       ├── rk356x-linux-20230524.tar.gz
│       └── ubuntu-rootfs-20230523.tar.gz
├── 2-系统镜像
│   └── RK3566 TX66
│       ├── Android11 TV
│       ├── Debian11
│       └── Ubuntu2004
├── 3-硬件资料
└── 4-开发工具
    ├── ADB工具
    ├── USB镜像烧录工具
    ├── USB驱动
    └── 串口调试工具
```





### Board Hardware

板卡照片（带功能标注）





### Accessory Information

**DC Power Interface**

配件连接图片



**USB OTG (Flashing USB Port)**

配件连接图片



**Debug Serial Port**

硬件配件连接图片





## 2. Image Flashing

The development board comes preloaded with the default image. If you don't need to change the system, you can skip this step and start the system.



### Installing the Image Flashing Tool

Image flashing tool directory on the cloud drive：4-开发工具\USB镜像烧录工具\RKDevTool\_Release\_v2.96.zip

![image-20230526154741821](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154741821.png)





### Installing USB Drivers

USB drivers directory on the cloud drive：4-开发工具\USB驱动\DriverAssitant\_v5.12.zip

![image-20230526154517334](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154517334.png)





### Obtaining Image Files

You can obtain image files through the following two methods: downloading from the cloud drive or compiling from the SDK.

For detailed steps on SDK compilation, refer to the document [02初级篇-sdk编译]

Image files directory on the cloud drive:

```
├── 2-系统镜像
    └── RK3566-TX66
        ├── Android11-TV
        ├── Debian11
        └── Ubuntu2004
```





### Putting the Development Board in Flashing Mode

Steps:

1. Connect the board's USB OTG to the PC's USB port.
2. Press and hold the RECOVERY button.
3. Connect the 12V DC power interface.
4. Open the flashing tool and check if the LOADER device is detected.





### Using the Flashing Tool

Steps:

1. The flashing tool successfully detects the board device (if not detected, go back to the previous step and confirm the operation).
2. Enter the full image flashing page.
3. Select the image path.
4. Start flashing the image.
5. Check the flashing log and the system will automatically restart after successful flashing.

![image-20230519111357204](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230519111357204.png)







## 3. System Startup

### Graphical Desktop Login

Connect HDMI or LCD.





### Debug Serial Port Login





### Android ADB Login
