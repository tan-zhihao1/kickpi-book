# 02-K1-Quick_Start_Guide

This guide is designed to assist users in quickly getting started with the motherboard, covering topics such as powering on the board, connecting a display, using the debug serial port, ADB, and setting up SSH connections.

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the motherboard's command-line console. [Motherboard Command-Line Console](#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally referring to an environment where ADB can be run.

## Powering on the Board

Currently, all series of boards are set to boot automatically upon power-on by default. The following section introduces the power supply methods for each board and the signs of normal board startup.

### **Power Supply**

The K1 motherboard only supports 12V power supply. The power supply method is through a DC12V socket, and the motherboard is powered by connecting a 12V power adapter.

* Actual wiring diagram

![image-20250416190745103](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416190745103.png)

### LED Indicators

The development board is equipped with 2 LEDs:

The green LED is the power indicator, which is normally on when powered on.
The blue LED is the system heartbeat indicator. If the heartbeat LED indicator flashes continuously, it indicates that the kernel is running normally.
Additionally, the system heartbeat indicator can be controlled for other functions as described below.

### **Power-On Steps**

1. Plug in the DC12V power supply to  board.
2. check the status of the two LED lights:
   - The power indicator should be constantly on when powered on. If it is not on, check if there are any abnormalities in the power supply section.
   - The heartbeat light should flash continuously after the kernel starts. If it does not flash, the system may be running abnormally.

## Display

The K1 motherboard has display interfaces. The number of interfaces and the default compatible display types can be found in the [K1 Hardware Introduction](../01-Development_board_Introduction/02-KICKPI-K1_Hardware_Introduction.md#display) section. You can connect display peripherals as needed.

**All display interfaces on the KICKPI development board are used with the corresponding display adapter boards. The adapter boards will be pre-connected and shipped when you purchase the display.**

### LVDS/MIPI

The K1 is default compatible with LVDS 7-inch screens, MIPI 5-inch, 8-inch, and 10.1-inch screens sold by KICKPI.

* Wiring method:

LVDS/MIPI 0 interface: [Flip-down FPC interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-1)

![image-20250512100410963](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512100410963.png)

LVDS/MIPI 1 interface: [Pull-up FPC interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-2)

![image-20250512100640644](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512100640644.png)

### eDP

The K1 board is designed with one eDP screen interface.

eDP interface: [Flip-down FPC interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-1)

![image-20250512141137367](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512141137367.png)

### HDMI

The motherboard is equipped with an HDMI OUT display interface. The interface type is HDMI Type-A, which supports hot-plugging.

![image-20250421112729945](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250421112729945.png)

## User and Password

### User Information

The default usernames and passwords for different systems are as follows:

| System | Username | Password |
| ------ | ------ | ------ |
| Debian | linaro | linaro |
| Debian | root |  |
| Ubuntu | kickpi | kickpi |
| Ubuntu | root |  |

> The root password is not set and needs to be set manually.

### Changing the User Password

```
console$ passwd $username
```

Example:

```
root@kickpi:/# passwd kickpi
New password:
Retype new password:
passwd: password updated successfully
```

## Debug Serial Port

The debug serial port is a UART port on the motherboard, mainly used during the development and debugging phases to help developers understand the internal working conditions of the system. The debug serial port is sometimes also referred to as the console port or the debug terminal.

**Baud rate: 1500000** 

> The USB-to-UART cable connected to the socket also needs to support a baud rate of 1500000 for normal communication.

**Debug Serial Cable Description**

The currently provided debug serial cable has four wires. The red one is `VCC`, the green one is `TX`, the white one is `RX`, and the black one is `GND`. Generally, there is no need to connect the `VCC`.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

> The same applies to other debug tool cables. Only connect TX, RX, and GND.

**Viewing the DEBUG Pins**

Each model of the motherboard is equipped with a DEBUG debug serial port, which is located in the 20PIN/40PIN pins. You can view the location of the DEBUG port in the **Expansion Pins** section of the hardware introduction for each model of the motherboard in the motherboard introduction. Additionally, the location is marked with silkscreen on the front and back of the board.

![image-20250422094320253](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422094320253.png)

**Connecting the Debug Serial Port of the Development Board**

Connect the `TX` of the debug cable to the `RX` of the motherboard, the `RX` of the debug cable to the `TX` of the motherboard, and the `GND` of the debug cable to the `GND` of the motherboard. There is no need to connect the `VCC`.

| Motherboard Expansion Pins | Debug Cable Pins |
| :------------ | ---------- |
| UART_RX_DEBUG | TX |
| UART_TX_DEBUG | RX |
| GND | GND |

**Downloading the Serial Assistant Software**

There are many free/commercial serial assistant tools, no need to use a special one, you can choose the one you preferred. Here we use Mobaxterm as an example:

Mobaxterm software download link : https://mobaxterm.mobatek.net/

**Mobaxterm Setup Steps**

1. Open Mobaxterm and click the "Session" button to create a new session window.
2. In the session type list, select "Serial".
3. Select the serial port COM index, which can be retrieved by opening the Windows System Device Manager -> COM and LPT.
4. Set the serial port baud rate to 1500000.
5. Click "OK" to start the session window.

![image-20250418175523261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418175523261.png)

## ADB

The K1 Android/Debian system supports the use of the ADB function. For details, refer to the [ADB Tutorial](../../../common/zh/adb/ADB_Tutorial.md).

## SSH

The K1 supports the use of the SSH function. For details, refer to [SSH](../../../common/zh/linux/SSH.md).

## Common Issues

### **Power Supply Issue**

When the motherboard is connected to multiple peripherals, resulting in high power consumption, after connecting the power supply, the power-on green light may be dim or not remain constantly on, and the system startup may also be abnormal.

**Solution**: This is generally a power supply issue. Please check if the power consumption of the corresponding power supply is sufficient to support the power supply of the connected peripheral modules.

### **MIPI Screen Display Issue**

If the MIPI screen fails to display, check if the wiring is correct and if the backlight is lit normally.

**Solution**:

If the backlight is not lit, check the wiring. [MIPI_DSI Wiring](#MIPIDSI)
If the backlight is lit, check the software. (K7 V1.2 does not support automatic recognition of MIPI screens yet. Different software needs to be burned for corresponding screens.)

### **Debug Serial Port Usage Issue**

When using Mobaxterm software or other serial port tools to connect to the serial port, you may find that the serial port displays garbled characters or can only display but cannot be written to.

**Solution**: This is generally because the debug serial port tool does not support a baud rate of 1500000. It is recommended to use a debug serial port tool that supports a baud rate of 1500000.

### **Username and Password Error Issue**

When logging in via ssh, the password is incorrect.

**Solution**: Please confirm if the relevant account exists and if the password is correct. You can use the following command to change the user password:

```
console$ passwd $username
```

Example:

```
root@kickpi:/# passwd kickpi
New password:
Retype new password:
passwd: password updated successfully
```

### What is the Motherboard Command-Line Console? <a id="console_readme"> </a>

The command-line console generally refers to an operating interface in the documentation that allows users to interact with the computer system through text commands. Users can perform operations (such as file management, program execution, and system configuration) by entering specific instructions, rather than relying on the graphical user interface (GUI) for mouse clicks and visual operations.

Examples of command-line consoles:

For the Android system, you can use the serial port or adb shell to use the command-line console.

```
console$
```

For the Debian system, you can use the serial port, adb shell, ssh, etc., to use the command-line console.

```
root@linaro$
```

For the Ubuntu system, you can use the serial port, adb shell, ssh, etc., to use the command-line console.

```
kickpi@kickpi$
```