# 02-Quick_Start_Guide

This guide aims to help users quickly get started with the motherboard, covering power-on, display connection, debug serial port, ADB, SSH connections, and more.

## Powering On the Board

### Power Supply Methods

The K4B supports TYPE-C 5V power supply, as shown below:  

![image-20250520101200428](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520101200428.png)

### LED Status

The development board is equipped with two LEDs. By default:  
- **Green LED**: Power indicator (remains lit when powered on).  
- **Blue LED**: Heartbeat indicator (blinks continuously after the kernel boots).  

**Troubleshooting**:  
- If the power indicator is off, check the power supply.  
- If the heartbeat indicator does not blink, the system may be malfunctioning.  

## Display

The motherboard supports **LVDS** and **MIPI** displays.  
- The default system is configured for a **7-inch LVDS screen**. To switch to other displays, burn the corresponding image by following [Image Burning](../03-镜像烧录).  

**Important**: Always power off the motherboard before connecting a display.  

### Supported MIPI Displays
| **Model**      | **Size**      | **Resolution** | **Touch Support** |  
|----------------|---------------|----------------|-------------------|  
| AT101DS40I     | 10.1-inch     | 800x1280       | Y                 |  
| MX101BA1340    | 10.1-inch     | 800x1280       | Y                 |  
| MX080B2140     | 8.0-inch      | 800x1280       | Y                 |  
| F050008M01     | 5.0-inch      | 720x1280       | Y                 |  

### Supported LVDS Resolutions
| **Resolution** | **Touch Support** |  
|----------------|-------------------|  
| 1024x600       | Y                 |  

**Included 7-inch LVDS Screen**:  
- Model: MX070IBC  

**Hardware Wiring**:  
![image-20250520101443135](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520101443135.png)  

## Debug Serial Port

### Baud Rate
Set the debug serial port baud rate to **115200**.  

### Debug Cable Description
The provided debug cable has four wires:  
- **Red**: VCC (do not connect)  
- **Green**: TX  
- **White**: RX  
- **Black**: GND  

**Connection**: Only connect **TX, RX, and GND**. Ensure your debug tool supports **115200 baud rate**.  

![Serial port cable](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)  

### Debug Pins on the Board
The K4B debug pins are a separate 3-pin header (not part of the 30-pin expansion header):  
![image-20250520101759515](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520101759515.png)  

**Wiring Method**:  
![image-20250520102125388](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520102125388.png)  

### Serial Terminal Software Setup (MobaXterm)
**Download**:  
- Internal path: `5-DevelopmentTools\UART_DebugTool\MobaXterm_Portable_v20.3.zip`  
- Official site: https://mobaxterm.mobatek.net/  

**Configuration Steps**:  
1. Create a new session.  
2. Select "Serial" as the session type.  
3. Choose the COM port (check in Windows Device Manager > Ports).  
4. Set baud rate to **115200**.  
5. Start the session.  

![image-20250509173628697](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509173628697.png)  

## ADB  
ADB (Android Debug Bridge) is a debugging tool for Android systems. It supports USB or network connections for app installation, file transfer, and command execution.  
- For usage details, see [ADB Tutorial](../../../common/zh/adb/ADB教程.md).  

## SSH  
SSH (Secure Shell) enables secure remote access over a network.  
- To connect, obtain the board’s IP via the serial port or router admin page.  
- For instructions, see [SSH](../../../common/zh/linux/SSH.md).  

![image-20250411103217477](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250411103217477.png)  

## FAQ  

### Power Supply Issues  
**Symptom**: The green LED dims or flickers when connecting high-power peripherals, causing system instability.  
**Solution**: Ensure the power supply meets the total power requirements of all connected peripherals.