# 01-USB_Cable_Flashing

## 1. Image Flashing

The development board comes pre - flashed with an image by default. If you don't need to replace the system, you can skip this step and start the system directly.

### Download Preparation Tools

The tools required for image flashing are as follows:

```
├── System Image
│   └── RK3568 - K1
│       ├── Android13
│       ├── Debian11
│       └── Ubuntu2004
└── Development Tools
    ├── USB Driver
    └── USB Image Burn Tool
```

> For image flashing, you must download the USB driver and the image burn tool.
>
> You can choose to download the system image according to your actual needs or compile it yourself through the SDK.

### Install the USB Driver

The cloud disk path of the USB driver is:

```
DevelopmentTools\USB Driver\DriverAssitant_v5.12.zip
```

![image-20230526154517334](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154517334.png)

### Obtain the Image File

You can obtain the image file in two ways: download from the cloud disk or compile through the SDK.

Refer to the document [SDK Source Code Compilation](11-SDK源码编译.md) for the detailed steps of SDK compilation.

The network disk directory of the image file is:

```
├── Image
    └── RK3568 - K1
        ├── Android13
            ├── update-rk3568-kickpi-k1-android-hdmi-2023102403.img
        ├── Debian11
        └── Ubuntu2004
```

Image name parsing:

![image-20231024111614065](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231024111614065.png)

### Install the Image Burn Tool

The network disk directory of the image burn tool is:

```
DevelopmentTools\USB Image Burn Tool\RKDevTool_v3.30.zip
```

1. After downloading the image burn tool from the network disk, unzip the folder to get the following directory list:

![image-20241025184222743](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025184222743.png)

> Please read the development tool usage documentation carefully for tool usage.

2. Modify the language of the image burn tool (optional)

The default interface of the image burn tool is in Chinese. You can modify it to the English interface as follows:

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010202819610.png)

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010203036869.png)

3. Open the burn tool

![image-20231017114016561](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231017114016561.png)

4. The interface of the burn tool is as follows:

![image-20250428105459703](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105459703.png)

### Enter the Flashing Mode of the Development Board (Pic)<a id="burn_mode"> </a>

To flash an image, you must make the development board enter the flashing mode. The flashing modes of the Rockchip platform include LOADER mode and Maskrom mode.

The LOADER mode is commonly used for both full image flashing and partition flashing.

The Maskrom mode is commonly used for forced image flashing after the system is damaged.

> You can view the buttons in the development board information section of the KICKPI - Hardware Introduction.

Operation steps:

1. Open the burn tool on a Windows computer (refer to the above instructions to install the burn tool).
2. Connect the OTG interface of the development board to the USB interface of the computer.
3. Keep pressing the Loader button and connect the DC power supply interface for power - on operation (if it is already powered on, you can press the reset button for reset operation).
4. Check whether the image burn tool can correctly recognize the Loader device/Maskrom device.
5. If the device is correctly recognized, you can release the Loader button. Otherwise, repeat the above steps and try several more times.

> To enter the Maskrom mode, repeat the operation steps, but note that in step 3, press and hold the MASKROM button on the back of the board instead of the Loader button.

### Flash the Image

Image flashing is divided into full image flashing and partition image flashing.

Full image flashing is commonly used for system flashing or replacing different systems on the development board.

Partition image flashing is commonly used in the debugging stage. You must clearly understand the function of each partition before flashing; otherwise, the system may be damaged.

#### Flash the Full Image

![image-20250428105322719](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105322719.png)

Operation steps:

1. Enter the full image flashing page.
2. Select the image path.
3. Make sure the burn tool correctly recognizes the board device [enter the flashing mode](#burn_mode).
4. Start flashing the image.
5. Check the flashing log. After successful flashing, the system will automatically restart.

#### Flash the Partition Image

Partition images are mainly used for debugging normal compiled images. Multi - screen adaptive images cannot be replaced by flashing partition images. All the firmware in the network disk are multi - screen adaptive images.

![image-20250428105212038](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105212038.png)

Operation steps:

1. Enter the partition image flashing page.
2. Check the partition items to be flashed (the Parameter must be checked).
3. Select the storage path of the corresponding file.
4. Make sure the burn tool correctly recognizes the board device [enter the loader mode](#burn_mode).
5. Start flashing the image.
6. Check the flashing log. After successful flashing, the system will automatically restart.