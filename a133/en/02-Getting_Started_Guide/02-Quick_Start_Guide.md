# 02-Quick_Start_Guide

This guide is designed to help users quickly get started with the motherboard, including board power-on, display connection, debug serial port, ADB, SSH connection, and other contents.



## Board Power-on

### Power Supply Methods



### LED Status  
- **Green LED**: Power indicator (steady on when powered).  
- **Blue LED**: Heartbeat indicator (blinks after kernel boot).  
- Troubleshooting:  
  - No green light: Check power supply.  
  - No blue light blinking: System malfunction.  



## Display





## Debug UART

### Baud Rate 

Set to **115200**.

### Debug Serial Cable Description

UART Debug dongle has 4 wires：

- Green: TX  
- White: RX  
- Black: GND  
- Red: VCC (do not connect)

![Serial port cable](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

### Board DEBUG Pins











### Wiring Method



### Serial Port Assistant Software Download and Use

**Download Serial Port Assistant Software**

The serial port assistant software can be downloaded by yourself. Take Mobaxterm as an example below.

location in cloud disk：

``` 
5-DevelopmentTools\UART_DebugTool\MobaXterm_Portable_v20.3.zip
```

Software official website download:https://mobaxterm.mobatek.net/



**Mobaxterm Operation steps**

1. Create a new session window.
2. Select the session window type as "serial".
3. Select the serial port COM number (open the Windows Device Manager -> Ports interface to view the COM number).
4. Select the serial port baud rate as 115200.
5. Start the session window.

![image-20250509173628697](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509173628697.png)

## ADB

ADB (Android Debug Bridge) is a debugging bridge tool for the Android system, which supports connecting to devices via USB or network, and can realize device management and debugging operations such as application installation, file transfer, and command execution.

Android system supports ADB function, see details [ADB_Tutorial](../../../common/en/adb/ADB_Tutorial.md)

### 

![image-20250509185455550](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509185455550.png)

## SSH

SSH (Secure Shell) is a network protocol used to establish a secure remote connection on an insecure network. It provides encryption and authentication mechanisms to ensure the confidentiality and integrity of data. Through SSH, users can securely manage remote servers, transfer files, and access remote resources.

Connect the board to the network and view the board's IP address through the serial port or the router's background.

**There are three ways to view the IP address after the development board starts:：**

1. **Connect an HDMI monitor, log in to the system, and use the ifconfig eth0 command to view the IP address.**
2. **Enter the ifconfig eth0 command in the debug serial port terminal to view the IP address.**
3. **If there is no debug serial port or HDMI monitor, you can also view the IP address of the development board's network port through the router's management interface. However, many people often cannot see the development board's IP address normally using this method. If you cannot see it, the debugging methods are as follows:**

1. **First, check whether the Linux system has started normally. If the blue light of the development board is flashing, it usually means the system has started normally; otherwise, the system has not started normally.**
2. **Check whether the network cable is plugged in tightly, or try replacing the network cable.**
3. **Try replacing the router (many problems with routers have been encountered, such as the router being unable to assign an IP address normally, or having assigned an IP address normally but not being visible in the router).**
4. **If there is no router to replace, you can only connect an HDMI monitor or use the debug serial port to view the IP address.**


**In addition, it should be noted that the development board's DHCP automatic IP address allocation does not require any settings.**

### User Information

The default username and password are as follows:

| System   | Username | Password   |
| ------ | ------ | ------ |
| Ubuntu | kickpi | kickpi |
| Ubuntu | root   | root   |

``` shell
ssh kickpi@<ip>
Password: kickpi
```

**Precautions**

- Ensure that the motherboard's network connection is normal.
- Ensure that the computer can ping the motherboard's IP address normally.
- Ensure that the entered username and password exist and are correct.

![image-20250411103217477](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250411103217477.png)



## FAQ

### Power Supply Issues

When the motherboard is connected to many peripherals, resulting in high power consumption, after connecting the power supply, the power-on green light may be dim or not remain on, and the system startup may also be abnormal.

**Solution**：This is generally a power supply problem. Please check whether the power consumption of the corresponding power supply is sufficient and whether it can support the power supply of the connected peripheral modules.



### ADB Device Not Recognized

When debugging ADB, check whether the following device is recognized in the System Device Manager when the board's blue light is flashing.

![image-20250509185304921](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509185304921.png)

If an unknown device is recognized, as shown in the figure, you need to manually install the driver.

![image-20250226152046272](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152046272.png)



![370849870429aae4906079eb686ee326](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/370849870429aae4906079eb686ee326.jpg)

![image-20250226152541205](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152541205.png)

![image-20250226152555933](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152555933.png)

![image-20250226152618854](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152618854.png)

![image-20250226152635425](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152635425.png)



![image-20250226152643858](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152643858.png)

![image-20250226152654260](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152654260.png)
