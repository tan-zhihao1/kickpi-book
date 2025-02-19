# 02-Image Burnning

K2B currently provides two images: Android 12.0 and Ubuntu 22.04.

For Android 12.0, you can use a USB cable to flash the image onto the onboard eMMC.

For Ubuntu 22.04, you need to either flash the image onto an SD card and boot the system directly from the SD card or copy the system from the SD card to the eMMC.



## 1. Android Image Burnning

The development board comes with pre-installed images by default. 

If you don't need to change the system, you can skip this step and start the system directly.



### Tool Preparation

**Downloading Software Tools**

The software tools are divided into three parts: USB drivers, image burning tools, and system images.

All three parts can be downloaded from the cloud drive. Please contact customer service to obtain the cloud drive address.

Below is the directory structure corresponding to the tools in the cloud drive:

```
└── 2-System Image 系统镜像
    └── K2B
       	├── Android12 TV
       	├── Android12 Tablet
       	└── Ubuntu2204
└── 5-DevelopmentTools开发工具
    └── USB ImageBurnTool 镜像烧录工具
    	└── PhoenixSuit.zip
```

> Ways to Obtain System Images:
>
> 1. Download from the cloud drive.
> 2. Obtain through SDK compilation.

> Image Descriptions:
>
> The development board provides three images: Android 12 TV, Android 12 Tablet, and Ubuntu 20.04.
>
> Both TV and Tablet are compiled from the Android 12.0 SDK, with only the desktop interface being different.
>
> TV has a set-top box interface, while Tablet has a tablet interface. You can download them as needed.



**Hardware Preparation Tools**

Prepare a USB A to C burning cable to connect the burning host and the development board.



### Installing USB Driver

**Unzip PhoenixSuit.zip**

USB Driver Directory

```
PhoenixSuit/Drivers/AW_Driver/
```

> The driver does not support direct installation by opening it. You must first identify the unknown device in Device Manager before installing the driver.



**Entering Burning Mode on the Mainboard**

1. Press and hold the RECOVERY button on the board (or the MaskRom button on the back).
2. Power on the mainboard. If it's already powered on, power cycle it (press the reset button once).

![image-20250219154733095](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219154733095.png)

![image-20250219155121619](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155121619.png)

**Opening the Device Manager Interface on the Computer**

If the mainboard has successfully entered burning mode and the computer does not have the USB driver installed, it may recognize an "Unknown Device."

Follow the steps in the image below to install the driver.

![image-20250219155858913](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155858913.png)



![image-20250219155942874](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155942874.png)



![image-20250219160431151](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160431151.png)



![image-20250219160504707](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160504707.png)



### Installing Image Burning Tool

**Unzip and open PhoenixSuit.zip**

![image-20240306170759670](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170759670.png)



**Burning Tool Interface**

![image-20250219160538270](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160538270.png)



### Burning Image

**Selecting Image for Burning**

1. Enter the "Firmware" interface.
2. Click on "Image" to select the image.

![image-20250219160640908](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219160640908.png)



**Entering Burning Mode on the Mainboard**

1. Press and hold the RECOVERY button on the board (or the MaskRom button on the back).
2. Power on the mainboard. If it's already powered on, power cycle it (press the reset button once).

![image-20250219154733095](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219154733095.png)

![image-20250219155121619](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155121619.png)



**Start Burning Image**

1. Click "Yes" to start burning the image.
2. Wait for the burning progress bar to complete.
3. After the burning is completed, the mainboard will automatically restart.

![image-20240306171455427](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171455427.png)



### Android Desktop Interface

After the mainboard is connected to an HDMI display and the system successfully boots up, the interface will appear as follows:

![image-20240306171855522](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171855522.png)





## 2. Ubuntu Image Burning

### Tool Preparation

**Hardware Preparation Tools**

Prepare an SD card with a capacity of 16GB or more. It is recommended that the speed class of the SD card is C10 or higher. The transfer speed of the SD card will affect the time it takes for the development board to load the system.



**Downloading SD Card Burning Tool**

It is recommended to use balenaEtcher as the SD card burning tool. You can download it from the following directory in the cloud drive, or you can download it from the internet by yourself:

```
5-DevelopmentTools\SD Card Burning Tools\balenaEtcher.exe
```



**Downloading Image**

You can obtain the image from the following directory in the cloud drive:

```
└── 2-System Image
    └── K2B
       	└── Ubuntu2204
```



### Creating an SD Boot Card

![image-20240319142047775](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240319142047775.png)

1. Choose the image to burn.
2. Select the SD card disk to burn.
3. Start burning.



### Starting System from SD Card

1. Insert the SD card into the development board.
2. Power on the development board. The first boot from the SD card may take some time.

![image-20240321141105541](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240321141105541.png)



### Copying from SD Card to eMMC

1. Enter BananaPi config.

![image-20240321141417804](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240321141417804.png)

2. Enter the user password (password: kickpi).

![image-20240321141607099](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240321141607099.png)

3. After entering the BananaPi config interface, simply press Enter repeatedly.

![image-20240321141840923](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240321141840923.png)

4. Start copying the image to the eMMC.

![image-20240321142005534](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240321142005534.png)

5. Remove the SD card, restart the mainboard, and it will boot into the Ubuntu system normally.






### Linux system login username/password

Username/Password for Ubuntu system: kickpi/kickpi



