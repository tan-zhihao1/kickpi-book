# 03-USB_Cable_Flashing

This document is used for users to flash or upgrade the motherboard system.

## Image Flashing

### Install USB Driver <a id="USBDRV"> </a>

**Network Disk Path**

```
5-DevelopmentTool/win_x64_UsbDriver/DriverAssitant_v5.13.zip
```

1. Unzip the `DriverAssitant_v5.13.zip` and open the unzipped folder.
2. Find `DriverInstall.exe` and double-click to run it.

![image-20241025101533688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101533688.png)

3. Click "Driver Installation" and the installation will be successful.
(If an old version of the driver has been installed, you can uninstall the driver first and then install the driver to keep the driver version up-to-date.)

![image-20241025101606583](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101606583.png)

### Install RKDevTool

**Network Disk Path**

```
5-DevelopmentTool/win_x64_UsbImageBurnTool/RKDevTool_v3.30_for_window.zip
```

> The Windows firmware flashing tool for the RK3576 platform must use version V3.28 or higher.

1. Unzip the `RKDevTool_v3.30_for_window.zip` and open the unzipped folder.
2. Modify the language of the image flashing tool (optional)
The default interface of the image flashing tool is in Chinese. You can modify it to the English interface as shown below.

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010202819610.png)

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010203036869.png)

3. Find `RKDevTool.exe` and double-click to run it.

![image-20241025161627415](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025161627415.png)

### Flash the Image

**Enter the Flashing Mode of the Development Board (Pic) <a id="burn_mode"> </a>**

To flash the image, the development board must enter the flashing mode. The flashing modes of the Rockchip platform include LOADER mode and Maskrom mode.
- LOADER mode is commonly used for both full image flashing and partition flashing.
- Maskrom mode is commonly used for forcibly flashing the image after the system is damaged.

> You can view the buttons in the "KICKPI - Hardware Introduction" and "Development Board Information" sections.

Operation Steps:
1. Open the flashing tool on the Windows computer (refer to the above instructions to install RKDevTool).
2. Connect the OTG interface of the development board to the USB interface of the computer.
3. To enter **LOADER mode**, press the LOADER button and power on the board (if the motherboard is already powered on, you can press the reset button to reset it). To enter **MASKROM mode**, press the MASKROM button on the back of the board and power on the board (if the motherboard is already powered on, you can press the reset button to reset it).

> Both LOADER and MASKROM modes support full package flashing. You can choose one mode for flashing.

4. The Rockchip development tool should be able to recognize the LOADER device or MASKROM device. (You need to install the USB driver first to recognize the relevant devices.)

![image-20250422093529751](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093529751.png)

5. Click **Upgrade Firmware** to jump to the firmware upgrade interface.
6. Click **Firmware** and select the image `update-*.img` to be flashed. [Get the image file](01-获取镜像文件.md)

![image-20250422093506005](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093506005.png)

7. Click **Upgrade** and wait for the flashing to complete.

![image-20250422093623591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093623591.png)

8. The flashing is completed.

![image-20250422093934098](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093934098.png)

### Flash the Partition Image

The partition image is mainly used for debugging the normally compiled image. The multi-screen adaptive image cannot be replaced by flashing the partition image. The firmware in the network disk is all multi-screen adaptive image.

![image-20250428105212038](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105212038.png)

Operation Steps:
1. Enter the partition image flashing page.
2. Check the partition items to be flashed (the partition file `Parameter` must be checked to ensure the correct partition).
3. Select/enter the path where the corresponding file is stored.
4. The flashing tool successfully recognizes the board device [enter the loader mode](#burn_mode).
5. Start flashing the image.
6. Check the flashing log. After the flashing is successful, the system will automatically restart and enter the system.

## Factory Flashing

### Install FactoryTool

**Network Disk Path**

```
5-DevelopmentTool/FactoryTool/FactoryTool_v1.89.zip
```

1. Unzip the `FactoryTool_v1.89.zip` and open the unzipped folder.
2. Find `FactoryTool.exe` and double-click to run it.

![image-20250109172029374](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172029374.png)

### Flash the Image

1. Click **Firmware** and select the image `update-*.img` to be flashed.

![image-20250422101009222](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422101009222.png)

2. Click **Run** to start the factory flashing mode.
3. In the green light state, connect the motherboard with TYPE-C.
4. To enter **LOADER mode**, press the RECOVERY button and power on the board (if the motherboard is already powered on, you can press the reset button to reset it). To enter **MASKROM mode**, press the MASKROM button on the back of the board and power on the board (if the motherboard is already powered on, you can press the reset button to reset it). [Install the USB driver](#USBDRV)
5. Observe the light. When the device is recognized, the light will turn red. Do not unplug or plug in the device at this time.

![image-20250422103006729](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422103006729.png)

6. Wait for the green light. Repeat steps 3, 4, and 5 to flash new devices simultaneously.
7. After flashing all the devices, click **Stop**.

## Common Problems

### The Loader or Maskrom Mode is not Recognized

1. To enter **LOADER mode**, press the RECOVERY button and power on the board (if the motherboard is already powered on, you can press the reset button to reset it). To enter **MASKROM mode**, press the MASKROM button on the back of the board and power on the board (if the motherboard is already powered on, you can press the reset button to reset it).
2. Open the Device Manager on the computer.
3. If you can see the USB device, it means the USB driver is installed successfully.

![image-20241025101105194](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101105194.png)

If you cannot recognize the "Class for rockusb devices", you can try the following steps:
1. Open `DriverInstall.exe`, try to uninstall the driver, and then install the driver. [Install the USB driver](#USBDRV)
2. Contact the customer service for feedback.

### Use Other Functions of the Flashing Tool

For more instructions on the flashing tool, see

```
RKDevTool_v3.30_for_window/开发工具使用文档_v1.0.pdf
```