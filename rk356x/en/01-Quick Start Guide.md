# 01-Quick Start Guide



## 1. Introduction

### Cloud Drive Files

Baidu Cloud Drive Link：

OneDrive Cloud Drive Link：



* Directory Structure

```
├── 1-SDK Source 软件源码
│   ├── rk356x-android13
│   └── rk356x-linux
│       ├── buildroot-dl-20230607.tar.gz
│       ├── linaro-rootfs-20230524.tar.gz
│       ├── rk356x-linux-20230524.tar.gz
│       └── ubuntu-rootfs-20230523.tar.gz
├── 2-System Image 系统镜像
│   └── RK3566-TX66
│       ├── Android11-TV
│       ├── Android13
│       ├── Debian11
│       └── Ubuntu2004
├── 3-Hardware Data 硬件资料
└── 4-Development Tools 开发工具
    ├── ADB Tool
    ├── UART Debug Tool 串口调试工具
    ├── USB Driver USB驱动
    └── USB Image Burn Tool 镜像烧录工具
```





### Board Hardware（Pic）





### Accessory Information（Pic）

**DC Power Interface**

RK3568 K1 comes with a 12V adapter

RK3566 TX66 comes with a 5V adapter



**USB OTG (Flashing USB Port)**





**Debug Serial Port**







## 2. Image Burnning

The development board comes preloaded with the default image. If you don't need to change the system, you can skip this step and start the system.



### Installing the Image Burnning Tool

Image burnning tool directory on the cloud drive：

​	4-Development Tools\USB Image Burn Tool\RKDevTool\_Release\_v2.96.zip



![](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154741821.png)



**Change the language of the burning tool to English**

![image-20231010202819610](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010202819610.png)

![image-20231010203036869](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010203036869.png)



### Installing USB Drivers

USB drivers directory on the cloud drive：

​	4-Development Tools\USB Driver\DriverAssitant\_v5.12.zip



![image-20231010203215046](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010203215046.png)



### Obtaining Image Files

You can obtain image files through the following two methods: downloading from the cloud drive or compiling from the SDK.

For detailed steps on SDK compilation, refer to the document [02-SDK Compilation]

Image files directory on the cloud drive:

```
├── 2-System Image 系统镜像
│   └── RK3566-TX66
│       ├── Android11-TV
│       ├── Android13
│       ├── Debian11
│       └── Ubuntu2004
```





### Putting the Development Board in Flashing Mode

Steps:

1. Connect the board's USB OTG to the PC's USB port.
2. Press and hold the RECOVERY button.
3. Connect the 12V DC power interface.
4. Open the flashing tool and check if the LOADER device is detected.

![image-20230926172750849](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230926172750849.png)



### Using the Burnning Tool

#### Burnning complete image

Steps:

1. The burnning tool successfully detects the board device (if not detected, go back to the previous step and confirm the operation).
2. Enter the complete image brunning page.
3. Select the image path.
4. Start burnning the image.
5. Check the burnning log and the system will automatically restart after successful flashing.



![image-20230519111357204](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230519111357204.png)



#### Burnning partition image

Steps:

1. The burnning tool successfully detects the board device (if not detected, go back to the previous step and confirm the operation).
2. Enter the partition image brunning page.
3. Select the partition items for burnning
4. Select the image path.
5. Start burnning the image.
6. Check the burnning log and the system will automatically restart after successful flashing.



![image-20230926170256112](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230926170256112.png)



## 3. System Startup

### Graphical Desktop Login

Connect HDMI or LCD.





### Debug Serial Port Login





### Android ADB Login
