# 10-System image burning

[TOC]

K5 currently provides Android 10.0 and ubuntu 16.04 images. You can use a USB burning cable to burn the images to the onboard eMMC.




## mirror burning

The development board burns the image by default when it leaves the factory. If you don't need to replace the system, you can skip this step directly and start the system. Android and Ubuntu burn in the same way, and the selected image is different.



### preparation tool

Download software tools

Software tools are divided into three parts, namely USB driver, mirror burning tool, and system image

All three parts can be downloaded through the online disk, and the online disk address can be obtained by contacting customer service.

The following is the path corresponding to the tool in the network disk

```
5-DevelopmentTool开发工具
```

> How to obtain the system image: 
>
> 1. Download from the network disk 
> 1. Get it by compiling the SDK

> Mirror description:
>
> ​	The development board provides 2 mirrors
>
> ​			Android10.0 Tablet 平板
>
> ​			Ubuntu 16.04



**Hardware Preparation Tool**

Prepare a USB A to C programming cable for connecting the programming host to the development board



## Prepare for mirror image

Download mirror path

```
2-Image镜像\K5\Android10.0\
2-Image镜像\K5\Ubuntu1604\
```

explain

```
a133_android10_c3_uart0-lvds-7-*.img
a133_android10_c3_uart0-lvds-21-*.img
a133_linux_c3_uart0_lvds-7-1024-600-ubuntu1604_xfce_*.img

According to the img name entry, you can select the corresponding mirror for burning.
	android10 - Representative, Android 10.0 Tablet System
	ubuntu1604 - On behalf of Ubuntu 16.04 system
	lvds-7-1024-600 Representative, the software is adapted to the signal lvds 1024 * 600 screen (internal screen)
	lvds-21-1920-1080 Representative, the software is adapted to dual lvds 1920 * 1080 screen 
	xfce - Represents the xfce desktop
```





### Install the USB driver

**Unzip PhoenixSuit.zip**

USB driver directory

```
PhoenixSuit/Drivers/AW_Driver/
```

> The driver does not support direct opening and installation. The driver can only be installed after the unknown device is identified in the Facility Management



**Serial port mode enters burn mode**

1. Serial input press and hold 2
2. The main board is powered on, if it is powered on, it needs to be powered on again (press the reset button)



**Press the button to enter the burn mode**

1. Press and hold the FEL upgrade button
2. The main board is powered on, if it is powered on, it needs to be powered on again (press the reset button)



**Open the Facility Management interface**

If the main board successfully enters the burn mode and the computer does not have a USB driver, the unknown device can be recognized

Follow the steps shown in the figure below to install the driver.

![image-20221129104615672](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104615672.png)



![image-20221129104636689](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104636689.png)



![image-20221129104649695](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104649695.png)



![image-20221129104706378](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104706378.png)



![image-20221129104725698](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104725698.png)



### Install the mirror burning tool

**Extract PhoenixSuit.zip and open PhoenixSuit.exe.**

![image-20240306170759670](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170759670.png)

**Burning tool interface**

![image-20240306170835586](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170835586.png)



### Burn mirror

**Select the burned image.**

1. enter `一键刷机` interface
2. click `浏览` Select mirror

![image-20240306171236404](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171236404.png)



**The mainboard enters the burning mode.**

Operation steps:

1. Press and hold the FEL upgrade key.
2. The main board is powered on, if it is powered on, it needs to be powered on again (press the reset button)



**Start burning the image.**

1. click `是` Start burning mirror image

2. Wait for the burning progress bar to complete
3. After burning, the main board automatically restarts

![image-20240306171455427](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171455427.png)



### Android desktop interface

According to the mirror image of the corresponding screen, the main board is connected to the corresponding LVDS screen. After the system start-up is successful, the interface is as follows

![Screenshot_20240824-164757](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/Screenshot_20240824-164757.png)
