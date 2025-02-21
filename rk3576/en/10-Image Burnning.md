# 10-Image Burning



## Instructions

SDK - refers to the source code. For the specific name, please refer to the SDK Source Code Compilation.



## Image Burning

### Path of AndroidTool

The tool can be found in the source code. A backup copy is available in the cloud disk under 5 - Development Tool.
The USB driver of Android 14.0, DriverAssitant, needs to be updated to version V5.1.1. You can refer to the tool section below for the update.
For the RK3576 platform, the Windows firmware burning tool must be version V3.28 or above.
Windows Burning Tool，path in the cloud disk:

```
5-DevelopmentTool
```



### Installing the USB Driver

**USB Driver Path**

```
5-DevelopmentTool/USB Driver/DriverAssitant_v5.13.zip
```

1. Unzip DriverAssitant_v5.13.zip and open the unzipped folder.

2. Find DriverInstall.exe and double - click to run it.

![image-20241025101533688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101533688.png)

3. Click "Install Driver" and a prompt indicating successful installation will appear.

**USB Device Verification**

1. Power on the motherboard and prepare a type - c cable capable of file transfer.
2. Connect the motherboard to the PC using the type - c cable.

3. Open the Device Manager on the computer.

4. If the USB device can be seen (such as Class for rockusb devices), it means the USB driver is installed successfully.



**Non - recognition Issue**

1. Confirm that the motherboard is powered on.

2. Open DriverInstall.exe, try to uninstall the driver, and then reinstall it.
3. Contact the customer service for feedback and consultation.



### Using the Burning Tool

**Burning Tool Path**

```
5-DevelopmentTool/USB ImageBurnTool/RKDevTool_v3.30_for_window.zip
```

Unzip the RKDevTool_v3.30_for_window.zip compressed file, find RKDevTool.exe under the file, and double - click to open it.



#### **整包烧录**

LOADER / MASKROM Mode

1. 
For `LOADER Mode`, press the RECOVERY button and power on (or press the reset button to reset). For `MASKROM Mode`, press the MASKROM button on the back of the board and power on (or press the reset button to reset).

2. The RKDevTool.exe can recognize the LOADER device or MASKROM device.

3. Click "Upgrade Firmware" to jump to the firmware upgrade interface.

4. Click "Firmware" and select the image to be burned, update - *.img.

5. Click "Upgrade" and wait for the burning to complete.

6. Restart the device.




## 工厂烧录

```
5-DevelopmentTool开发工具/FactoryTool 工厂镜像烧录工具/FactoryTool_v1.89.zip
```
打开软件

![image-20250109172029374](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172029374.png)

选择固件，进行烧录，接入主板后，使主板进入 MASKROM / LOADER 模式

![image-20250109172203575](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172203575.png)
