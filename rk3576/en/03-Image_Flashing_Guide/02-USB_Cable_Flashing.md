# 02-USB_Cable_Flashing

This document is used for users to burn or upgrade the motherboard system.

## Image Flashing

### Install USB Driver <a id="USBDRV"></a>

**Network Disk Path**

```
5-DevelopmentTool/USB Driver USB/DriverAssitant_v5.13.zip
```

> The USB driver DriverAssitant for Android14.0 needs to be updated to version V5.1.1.

1. Unzip the `DriverAssitant_v5.13.zip` and open the unzipped folder.
2. Find `DriverInstall.exe` and double-click to run it.

![image-20241025101533688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101533688.png)

3. Click "Driver Installation". When the installation is successful, a prompt will appear.
(If an old version of the driver is already installed, you can uninstall the driver first and then install the driver again to keep the driver version up-to-date.)



### Install RKDevTool

**Network Disk Path**

```
5-DevelopmentTool/USB ImageBurnTool/RKDevTool_v3.30_for_window.zip
```

> For the RK3576 platform Windows firmware burning tool, version V3.28 or higher must be used.

1. Unzip the `RKDevTool_v3.30_for_window.zip` and open the unzipped folder.
2. Find `RKDevTool.exe` and double-click to run it.



### Start Image Flashing

1. To enter the **LOADER mode**, press the RECOVERY button and then power on the device (if the motherboard is already powered on, you can press the reset button to reset it). To enter the **MASKROM mode**, press the MASKROM button on the back of the board and then power on the device (if the motherboard is already powered on, you can press the reset button to reset it).
> Both the LOADER and MASKROM modes support full-package burning. You can choose one mode for burning.
2. The Rockchip development tool should be able to recognize the LOADER device or MASKROM device. (You need to install the USB driver first to recognize the relevant devices.)

![image-20250422093529751](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093529751.png)

3. Click **Upgrade Firmware** to jump to the firmware upgrade interface.
4. Click **Firmware** and select the image `update-*.img` to be burned. [Get the image file](01-获取镜像文件.md)

![image-20250422093506005](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093506005.png)

5. Click **Upgrade** and wait for the burning to complete.

![image-20250422093623591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093623591.png)

6. The burning is completed.

![image-20250422093934098](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093934098.png)

## Factory Flashing

### Install FactoryTool

**Network Disk Path**

```
5-DevelopmentTool/FactoryTool/FactoryTool_v1.89.zip
```

1. Unzip the `FactoryTool_v1.89.zip` and open the unzipped folder.
2. Find `FactoryTool.exe` and double-click to run it.

![image-20250109172029374](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172029374.png)

### Start Factory Flashing

1. Click **Firmware** and select the image `update-*.img` to be burned.

![image-20250422101009222](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422101009222.png)

2. Click **Run** to start the factory burning mode.
3. Connect the motherboard using a TYPE-C cable when the light is green.
4. To enter the **LOADER mode**, press the RECOVERY button and then power on the device (if the motherboard is already powered on, you can press the reset button to reset it). To enter the **MASKROM mode**, press the MASKROM button on the back of the board and then power on the device (if the motherboard is already powered on, you can press the reset button to reset it). [USB driver installation](#USBDRV)
5. Observe the light. When the device is recognized, the light will turn red. Do not unplug or plug in the device at this time.

![image-20250422103006729](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422103006729.png)

6. Wait for the light to turn green and repeat steps 3, 4, and 5 to burn new devices simultaneously.
7. After burning all the devices, click **Stop**.

## FAQ

### The Loader or Maskrom mode is not recognized

1. To enter the **LOADER mode**, press the RECOVERY button and then power on the device (if the motherboard is already powered on, you can press the reset button to reset it). To enter the **MASKROM mode**, press the MASKROM button on the back of the board and then power on the device (if the motherboard is already powered on, you can press the reset button to reset it).
2. Open the Device Manager on your computer.
3. If you can see the USB device, it means the USB driver is installed successfully.

If the "Class for rockusb devices" cannot be recognized, you can try the following steps:

1. Open `DriverInstall.exe`, try to uninstall the driver, and then install the driver again. [USB driver installation](#USBDRV)
2. Contact customer service for feedback and consultation.

