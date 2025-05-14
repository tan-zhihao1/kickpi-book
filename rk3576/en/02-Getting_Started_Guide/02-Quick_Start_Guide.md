
# 02-Quick_Start_Guide

This guide aims to help users quickly get started with using the motherboard, covering topics such as powering on the board, connecting the display screen, using the debugging serial port, ADB, and SSH connections.

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the command-line console of the motherboard. [Motherboard Command-Line Console](#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

## Powering on the Board

### **Power Supply Method**

The motherboard supports power supply through the DC12V power interface, as shown in the following figure:

![K7-DC12V](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250415192618127.png)

### Status Lights

The development board is equipped with two LED lights:
- The green LED is the power indicator.
- The blue LED is the heartbeat indicator.

![K7-LED](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416150032731.png)

### **Power-On Steps**

1. Plug in the circular output interface of the DC12V power supply to power on the board.
2. Initially judge the operating status of the development board through the status of the two LED lights:
   - The power indicator should stay lit after power-on. If it doesn't light up, check if there is an issue with the power supply.
   - The heartbeat light should flash continuously after the kernel starts. If it doesn't flash, the system may be running abnormally.

## Display Screen<a id="K7-LCD"> </a>

The motherboard supports three types of display interfaces: TYPE-C DP, HDMI OUT, and MIPI DSI. You can connect corresponding display screens to show the system interface.

### MIPI DSI

**Precautions**

Before connecting the MIPI DSI screen, make sure to power off the motherboard first to avoid damaging the device.

**Compatible Screens**

The motherboard is equipped with one MIPI DSI display interface, which is compatible with the following MIPI screens:

| **Model**    | **Size**  | **Resolution** | Touch Support |
| ----------- | --------- | ---------- | -------- |
| AT101DS40I  | 10.1-inch | 800x1280   | Y        |
| MX101BA1340 | 10.1-inch | 800x1280   | Y        |
| MX080B2140  | 8.0-inch  | 800x1280   | Y        |
| F050008M01  | 5.0-inch  | 720x1280   | Y        |

**Hardware Wiring**

When connecting the MIPI screen cable, make sure the blue side faces up and the white side faces down, and then connect it to the MIPI DSI interface. The specific wiring diagram is as follows:

![mipi0_01](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/mipi0_01.png)

![mipi0_02](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/mipi0_02.png)

### HDMI OUT

The motherboard is equipped with one HDMI OUT display interface, with a maximum output resolution of up to 4096x2160p120 and supports hot-plugging. Details of the HDMI interface are shown in the following figure:

![hdmi_01](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/hdmi_01.png)

### TYPE-C DP

The motherboard is equipped with one TYPE - C interface, which supports DP display function. Details of the TYPE - C interface are shown in the following figure:

![K7-TYPE C](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250409110346928.png)

## Debugging Serial Port

### Baud Rate

The baud rate of the debugging serial port needs to be set to 1500000.

### Explanation of the Debugging Serial Cable

Currently, the provided debugging serial cable has a total of four wires, and the functions of each wire are as follows:
- Red: VCC (no need to connect)
- Green: TX
- White: RX
- Black: GND

The connection method of other debugging tool cables is the same. You only need to connect TX, RX, and GND, and the debugging tool needs to support a baud rate of 1500000 to communicate normally. Details of the debugging serial cable are shown in the following figure:

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

### DEBUG Pins on the Board

* Pin numbering diagram:

![image-20250418172813571](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418172813571.png)

* Actual position diagram of the DEBUG pins:

![image-20250418173112794](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418173112794.png)

### Wiring Method

Connect the **TX** of the debugging cable to the **RX** on the motherboard, the **RX** of the debugging cable to the **TX** on the motherboard, and the **GND** of the debugging cable to the **GND** on the motherboard. Do not connect the **VCC**.

| Motherboard Expansion Pins | Debugging Cable Pins |
| :------------ | ---------- |
| UART_RX_DEBUG | TX         |
| UART_TX_DEBUG | RX         |
| GND           | GND        |

![image-20250418173402270](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418173402270.png)

### **Download and Use of the Serial Assistant Software**

You can download the serial assistant software by yourself. Here is an example using Mobaxterm:

1. **Download Address**: https://mobaxterm.mobatek.net/
2. Operation Steps:
   1. Open Mobaxterm and click the "Session" button to create a new session window.
   2. In the session type list, select "Serial".
   3. Select the serial port COM number, which can be viewed by opening the Windows System Device Manager -> Ports interface.
   4. Set the serial port baud rate to 1500000.
   5. Click "OK" to start the session window.

![image-20250418175523261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418175523261.png)



## ADB

The K7 supports the use of the ADB function. For details, see [ADB Tutorial](../../../common/en/adb/ADB_Tutorial.md).



## SSH

The K7 supports the use of the SSH function. For details, see [SSH](../../../common/en/linux/SSH.md).



## FAQ

### **Power Supply Issues after Power-On**

When the motherboard is connected to multiple peripherals, resulting in high power consumption, after connecting the power supply, the power-on green light may be dim or not stay lit, and the system startup may also be abnormal.

**Solution**: This is generally a power supply issue. Please check if the power consumption of the corresponding power supply is sufficient to support the power supply of the connected peripheral modules.



### **MIPI Screen Not Displaying Issue**

If the MIPI screen cannot display, you can check if the wiring is correct and if the backlight is lit normally.

**Solution**:

- If the backlight is not lit, check the wiring.
- If the backlight is lit, check the software. (K7 V1.2 does not support automatic recognition of MIPI screens yet. Different software needs to be flashed for corresponding screens.)



### **Abnormal Use of the Debugging Serial Port**

When using the Mobaxterm software or other serial port tools to connect to the serial port, you may find that the serial port has garbled characters or can only display but cannot write.

**Solution**: Generally, the debugging serial port tool does not support a baud rate of 1500000. It is recommended to use a debugging serial port tool that supports a baud rate of 1500000.



### What is the Motherboard Command-Line Console? <a id="console_readme"> </a>

The command-line console generally refers to the operating interface in the document that allows users to interact with the computer system through text commands. Users execute operations (such as file management, program running, system configuration, etc.) by entering specific instructions, rather than relying on the mouse clicks and visual operations of the graphical user interface (GUI).

Examples of command-line consoles:
- For the Android system, you can use the serial port or adb shell to use the command-line console.

```
console$
```
- For the Debian system, you can use the serial port, adb shell, SSH, etc. to use the command-line console.

```
root@linaro$
```
- For the Ubuntu system, you can use the serial port, adb shell, SSH, etc. to use the command-line console.

```
kickpi@kickpi$
```