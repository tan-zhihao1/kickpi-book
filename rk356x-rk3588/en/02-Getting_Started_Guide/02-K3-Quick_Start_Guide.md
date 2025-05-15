# 02-K3-Quick_Start_Guide

This guide is designed to help users quickly get started with the motherboard, covering topics such as powering on the board, connecting the display, using the debug serial port, ADB, and SSH connections.

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the motherboard's command-line console. [Motherboard Command-Line Console](#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

## Powering on the Board

Currently, all series of boards are set to boot automatically upon power-on by default. The following describes the power supply methods for each board and the signs of normal board startup.

### **Power Supply Method**

The K3 only supports 12V power supply through a DC12V socket. Connect a 12V power adapter to the motherboard to supply power.

![image-20250417192155136](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250417192155136.png)

### LED Indicators

The development board is equipped with two LEDs:

The green LED is the power indicator, which is normally on when powered on.
The blue LED is the system heartbeat indicator. If the heartbeat LED flashes continuously, it indicates that the kernel is running normally.

In addition, the system heartbeat LED can be controlled for other functions as described below.

### **Power-On Steps**

1. Plug in the DC12V power circular output interface to power on the board.
2. Initially judge the running status of the development board based on the status of the two LEDs:
   - The power indicator should be on when powered on. If it is not on, check the power supply section for abnormalities.
   - The heartbeat LED should flash continuously after the kernel starts. If it does not flash, the system may be running abnormally.

## Display

The display interfaces available on the K3 motherboard can be found in the [K3 Hardware Introduction](../01-Development_board_Introduction/04-KICKPI-K3_Hardware_Introduction.md#display) section, which shows the number of interfaces and the types of displays they are compatible with by default. You can choose to connect display peripherals as needed.

**The display interfaces on the KICKPI development board are all used with the corresponding display adapter boards. The adapter boards will be pre-connected and shipped when you purchase the display.**

### LVDS/MIPI

The K3 is compatible with the LVDS 7-inch screen, MIPI 5-inch, 8-inch, and 10.1-inch screens sold by KICKPI by default.

LVDS/MIPI interface: [Flip-type bottom-connect FPC interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-1)

![image-20250512142600611](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512142600611.png)

### Dual LVDS

1. Dual LVDS data interface
2. LVDS power supply voltage selection, options are 3.3V, 5V, and 12V. You must select the corresponding voltage according to the specification sheet; otherwise, the screen may not light up or may be damaged.
3. LVDS backlight power supply interface

![1746789001527](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1746789001527.png)

## User and Password

### User Information

The default usernames and passwords for different systems are as follows:

| System | Username | Password |
| ------ | ------ | ------ |
| Debian | linaro | linaro |
| Debian | root | |
| Ubuntu | kickpi | kickpi |
| Ubuntu | root | |

> The root password is not set by default and needs to be set manually.

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

The debug serial port is a UART port on the motherboard, mainly used during the development and debugging phases to help developers understand the internal working status of the system. The debug serial port is sometimes also called the console port or debug terminal.

**Baud rate: 1500000** 

> The USB-to-UART cable connected to the socket also needs to support a baud rate of 1500000 for normal communication.

**Debug Serial Cable Description**

The currently provided debug serial cable has four wires: red for `VCC`, green for `TX`, white for `RX`, and black for `GND`. Generally, there is no need to connect the `VCC` wire.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

> The same principle applies to other debug tool cables. Only connect TX, RX, and GND.

**Viewing DEBUG Pins**

Each model of the motherboard has a DEBUG debug serial port, which is located in the 20PIN/40PIN pins. You can view the location of the DEBUG port in the **Expansion Pins** section of the hardware introduction for each model of the motherboard in the motherboard introduction. The location is also marked with silkscreen on the front and back of the board.

![image-20250422094320253](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422094320253.png)

**Connecting the Debug Serial Port of the Development Board**

Connect the `TX` of the debug cable to the `RX` of the motherboard, the `RX` of the debug cable to the `TX` of the motherboard, and the `GND` of the debug cable to the `GND` of the motherboard. There is no need to connect the `VCC` wire.

| Motherboard Expansion Pins | Debug Cable Pins |
| :------------ | ---------- |
| UART_RX_DEBUG | TX |
| UART_TX_DEBUG | RX |
| GND | GND |

**Downloading Serial Assistant Software**

There are many free/commercial serial assistant tools, no need to use a special one, you can choose the one you preferred. Here we use Mobaxterm as an example.

Mobaxterm software download address: https://mobaxterm.mobatek.net/

**Mobaxterm Operation Steps**

1. Open Mobaxterm and click the "Session" button to create a new session window.
2. In the session type list, select "Serial".
3. Select the serial port COM index, which can be retrieved by checking the Windows System Device Manager -> COM and LPT.
4. Set the serial port baud rate to 1500000.
5. Click "OK" to start the session window.

![image-20250418175523261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418175523261.png)

## ADB

The K3 Android/Debian system supports the use of the ADB function. For details, see the  [ADB Tutorial](../../../common/zh/adb/ADB_Tutorial.md).

## SSH

The K3 supports the use of the SSH function. For details, see [SSH](../../../common/zh/linux/SSH.md).

## Common Issues

### **Power Supply Issue**

When the motherboard is connected to multiple peripherals, resulting in high power consumption, the power-on green LED may be dim or not stay on constantly after the power is connected, and the system startup may also be abnormal.

**Solution**: This is generally a power supply issue. Check whether the power consumption of the corresponding power supply is sufficient to support the power supply of the connected peripheral modules.

### **MIPI Screen Display Issue**

If the MIPI screen fails to display, check whether the wiring is correct and whether the backlight is lit normally.

**Solution**:

If the backlight is not lit, check the wiring. [MIPI_DSI Wiring](#MIPIDSI)
If the backlight is lit, check the software. (The K7 V1.2 does not support automatic MIPI screen recognition yet. Different software needs to be flashed for different screens.)

### **Debug Serial Port Usage Issue**

When using Mobaxterm software or other serial port tools to connect to the serial port, you may encounter issues such as garbled characters in the serial port or being able to display but not write.

**Solution**: This is generally because the debug serial port tool does not support a baud rate of 1500000. It is recommended to use a debug serial port tool that supports a baud rate of 1500000.

### **Username and Password Error Issue**

When logging in via ssh, the password may be incorrect.

**Solution**: Please confirm whether the relevant account exists and whether the password is correct. You can use the following command to change the user password:

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

The command-line console generally refers to an operating interface that allows users to interact with the computer system through text commands in the documentation. Users can execute operations (such as file management, program execution, system configuration, etc.) by entering specific instructions, rather than relying on graphical user interface (GUI) mouse clicks and visual operations.

Examples of command-line consoles:

For the Android system, you can use the serial port or adb shell to access the command-line console.

```
console$
```

For the Debian system, you can use the serial port, adb shell, or ssh to access the command-line console.

```
root@linaro$
```

For the Ubuntu system, you can use the serial port, adb shell, or ssh to access the command-line console.

```
kickpi@kickpi$
```