# 02-Quick_Start_Guide

This guide is designed to help users quickly get started with the motherboard, including board power-on, display connection, debug serial port, ADB, SSH connection, and other contents.

## Board Power-on

### Power Supply Methods

The K5C board supports multiple power supply methods. Verify hardware version compatibility.

| Power Supply Method | Voltage | Board Version |
| ------------------- | ------- | ------------- |
| DC                  | 12V     | >= V1.0       |
| Type-C              | 5V      | >= V1.1       |
| POE                 | 12V     | >= V1.0       |
| BAT                 | > 3.7V  | >= V1.2       |

**DC Power Supply**  

![image-20250322093752078](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250322093752078.png)![image-20250322093804013](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250322093804013.png)  

For other power supply details, refer to [K5C Board Power Supply](../08-Advanced/K5C主板供电.md).

### LED Status  
- **Green LED**: Power indicator (steady on when powered).  
- **Blue LED**: Heartbeat indicator (blinks after kernel boot).  
- Troubleshooting:  
  - No green light: Check power supply.  
  - No blue light blinking: System malfunction.  



## Display

The motherboard supports **LVDS screens** and **MIPI screens**.  

By default, the system is adapted for a **7-inch LVDS screen**. To switch to another display, refer to [Image Burning](../03-Image_Flashing_Guide) to burn the corresponding image.  

**Important Notes**  

- Ensure the motherboard is **powered off** before connecting the screen.  

---

Supported MIPI Screens  

| **Model**      | **Size**      | **Resolution** | **Touch Support** |  
| -------------- | ------------- | -------------- | ----------------- |  
| AT101DS40I     | 10.1-inch     | 800x1280       | Y                 |  
| MX101BA1340    | 10.1-inch     | 800x1280       | Y                 |  
| MX080B2140     | 8.0-inch      | 800x1280       | Y                 |  
| F050008M01     | 5.0-inch      | 720x1280       | Y                 |  

Supported LVDS Resolutions  

| **Resolution** | **Touch Support** |  
| -------------- | ----------------- |  
| 1024x600       | Y                 |  
| 1920x1080      | N                 |  

**Available 7-inch LVDS Screen**  
Model: `MX070IBC`  

---

**Hardware Connection**  

When connecting the screen’s FPC flex cable:  
- **Blue side** must face **up**.  
- **White side** must face **down**.  
- Insert into the **LVDS_LCD interface**.  

Connection diagram:  
![image-20250515104101028](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250515104101028.png)  



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

**K5C**

![image-20250515104514334](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250515104514334.png)

### Wiring Method

![image-20250515105006940](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250515105006940.png)

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

## SSH

SSH (Secure Shell) is a network protocol used to establish a secure remote connection on an insecure network. It provides encryption and authentication mechanisms to ensure the confidentiality and integrity of data. Through SSH, users can securely manage remote servers, transfer files, and access remote resources.

Support SSH function usage, see details [SSH](../../../common/en/linux/SSH.md)

![image-20250411103217477](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250411103217477.png)



## FAQ

### Power Supply Issues

When the motherboard is connected to many peripherals, resulting in high power consumption, after connecting the power supply, the power-on green light may be dim or not remain on, and the system startup may also be abnormal.

**Solution**：This is generally a power supply problem. Please check whether the power consumption of the corresponding power supply is sufficient and whether it can support the power supply of the connected peripheral modules.

