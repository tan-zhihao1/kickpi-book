# 10-system image burning



## 1. mirror burning

The development board is factory-default burned with an image. If there is no need to change the system, this step can be skipped directly and the system can be started.



### Download preparation tool

Mirror burning, the preparation tool is as follows

```
├── System Image
│   └── RK3568-K1
│       ├── Android13
│       ├── Debian11
│       └── Ubuntu2004
└── Development Tools
    ├── USB Driver
    └── USB Image Burn Tool
```

> Mirror burning, you must download the USB driver and mirror burning tool
>
> System image, you can choose to download according to actual needs, or compile through the SDK by yourself



### Install the USB driver

USB drive network disk path

```
DevelopmentTools\USB Driver \DriverAssitant_v5.12.zip
```

### Get image file

The image file can be obtained in the following two ways: network disk download and SDK compilation

SDK compilation detailed steps reference document [SDK source code compilation] (11-SDK source code compilation .md)

Mirror file network disk directory:

```
├──Image
    └── RK3568-K1
        ├── Android13
        	├── update-rk3568-kickpi-k1-android-hdmi-2023102403.img
        ├── Debian11
        └── Ubuntu2004
```



### Install the mirror burning tool

Mirror burning tool network disk directory

```
DevelopmentTools\USB ImageBurnTool \RKDevTool_v3.30.zip
```

1. After downloading the mirror burning tool through the network disk, unzip the folder and get the following directory list

![image-20250222152441341](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222152441341.png)

> Please read the power builder documentation carefully for tool use.

2. Modify the mirror burning tool language (optional)

The default mirror burning tool is the Chinese interface, which can be modified to the English interface as follows

![image-20250222152522197](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222152522197.png)

![image-20250222152551843](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222152551843.png)



3. Open burning tool

![image-20250222152619771](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222152619771.png)

4. The burning tool interface is as follows

![image-20250222152755759](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222152755759.png)



### The development board enters burn mode (Pic)

Burn the image, the development board must enter the burn mode. The burn mode of the Rockchip platform includes LOADER mode and Maskrom mode.

LOADER mode, often used for full image burning, partition burning can be

Maskrom mode, often used to force burning images after system damage

> The buttons can be viewed through the KICKPI-Hardware Introduction, Development Board Information section



Operation steps:

1. Open the burning tool on the Windows computer (refer to the above instructions to install the burning tool)

2. Development board OTG interface, connection, computer USB interface

3. Keep pressing and holding the RECOVERY button, and connect the DC power supply interface for power-on operation (if it has been powered on, you can press the reset button for reset operation)

4. Check whether the mirror burning tool recognizes the Loader device/Maskrom device normally

5. If the device is recognized normally, you can release the RECOVERY button, otherwise repeat the above steps and try a few more times

> Enter Maskrom mode, repeat the operation steps, and pay attention to step 3. Long press the MASKROM button on the back of the board, not the RECOVER button.



### Burn mirror image

Burning mirrors is divided into complete mirror burning and partition mirror burning

Complete mirror burning, often used for flashing, or replacing different systems with development boards

Partition image burning is often used in the debugging stage. The function of each partition must be clarified before burning, otherwise it will cause system damage



#### Burn the full mirror

![image-20250222154106294](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222154106294.png)

Operation steps:

1. The burning tool successfully recognizes the board device (if it cannot be recognized, return to the previous step to confirm the operation)
2. Enter the full mirror burning page
3. Select the mirror path
Step 4 Start burning the image
5. Check the burning log, and automatically restart and enter the system after successful burning



#### Burn partition image

![image-20250222155200560](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222155200560.png)

Operation steps:

1. Burn tool, successfully identify the board device (if not, return to the previous step to confirm the operation)
2. Enter the partition mirror burning page
3. Check the Burn partition item (be sure to check Parameter)
4. Select the mirror path
Step 5 Start burning the image
6. Check the burning log, and automatically restart and enter the system after successful burning



## 2. system start-up

### Linux system login user password

Debian system username/password: linaro/linaro

Ubuntu system username/password: kickpi/kickpi



### Graphical desktop login

Default automatic login kickpi user

The graphic desktop relies on the display device, and needs to be connected to HDMI or LCD before booting.



### Debugging serial port login

Default automatic login ROOT user

The ROOT user does not set a password by default. You can configure the ROOT password with the following commands

```
$ sudo passwd root
```



