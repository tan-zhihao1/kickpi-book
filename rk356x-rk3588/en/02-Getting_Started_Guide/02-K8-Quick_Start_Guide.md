# 02-K8-Quick_Start_Guide

This guide aims to assist users in quickly getting started with the motherboard. It covers topics such as powering on the board, connecting the display, using the debug serial port, ADB, and SSH connections.

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the motherboard's command-line console. [Motherboard Command-Line Console](#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally referring to an environment where ADB can be run.

## Powering on the Board

Currently, all series of boards are set to boot automatically upon power-on by default. The following section describes the power supply methods for each board and the signs of normal board startup.

### Power Supply Method

The K8 motherboard only supports 12V power supply. The power supply method is through a DC12V socket, which powers the motherboard by connecting a 12V power adapter.

* Actual wiring diagram

![image-20250513145856735](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250513145856735.png)

### LED Indicators

The development board is equipped with two LEDs:

The green LED is the power indicator, which stays lit when powered on.
The blue LED is the system heartbeat indicator. If the heartbeat light flashes continuously, it indicates that the kernel is running normally.
In addition, the system heartbeat indicator can be controlled for other functions as described below.

### Power-on Steps

1. Plug in the circular output interface of the DC12V power supply to power on the board.
2. Initially judge the operating status of the development board through the status of the two LED lights:
   - The power indicator should stay lit when powered on. If it doesn't, check the power supply for any abnormalities.
   - The heartbeat light should flash continuously after the kernel starts. If it doesn't, the system may be running abnormally.

## Display

The K8 motherboard has display interfaces. You can check the number of interfaces and the default compatible display types in the [K8 Hardware Introduction](../01-Motherboard_Introduction/05-KICKPI-K8_Hardware_Introduction.md#display) section. You can connect display peripherals as needed.

**The display interfaces of the KICKPI development board are all used with corresponding display adapter boards. The adapter boards will be pre-connected and shipped when you purchase the display.**

### MIPI

The K8 is default compatible with the MIPI 5-inch, 8-inch, 10.1-inch, etc. displays sold by KICKPI.

* Wiring method:

MIPI 0 interface: [Flip-down FPC interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-1)

![image-20250513145744893](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250513145744893.png)

MIPI 1 interface: [Pull-up FPC interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-2)

![image-20250513145800459](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250513145800459.png)

### HDMI OUT

The motherboard is equipped with two HDMI OUT display interfaces. The interface type is HDMI-A, which supports hot-plugging.

![image-20250513145931050](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250513145931050.png)

### HDMI IN

There is one HDMI IN display interface. The interface type is HDMI-A, which supports hot-plugging.

![image-20250513150004888](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250513150004888.png)

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

### Changing User Password

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

The debug serial port is a UART port on the motherboard, mainly used during the development and debugging phases to help developers understand the internal working status of the system. The debug serial port is sometimes also referred to as the console port or debug terminal.

**Baud rate: 1500000**

> The USB-to-UART cable connected to the socket also needs to support a baud rate of 1500000 for normal communication.

**Debug Serial Cable Description**

The currently provided debug serial cable has four wires. The red wire is `VCC`, the green wire is `TX`, the white wire is `RX`, and the black wire is `GND`. Generally, there is no need to connect `VCC`.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

> The same applies to other debug tool cables. Only connect TX, RX, and GND.

**Viewing DEBUG Pins**

Each model of motherboard is equipped with a DEBUG debug serial port, which is located in the 20PIN/40PIN pins. You can view the location of the DEBUG port in the **Expansion Pins** section of the hardware introduction of each model in the motherboard introduction. The location is also marked with silkscreen on the front and back of the board.

![image-20250422094320253](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422094320253.png)

**Connecting the Debug Serial Port of the Development Board**

Connect the `TX` of the debug cable to the `RX` of the motherboard, the `RX` of the debug cable to the `TX` of the motherboard, and the `GND` of the debug cable to the `GND` of the motherboard. There is no need to connect `VCC`.

| Motherboard Expansion Pins | Debug Cable Pins |
| :------------ | ---------- |
| UART_RX_DEBUG | TX |
| UART_TX_DEBUG | RX |
| GND | GND |

**Downloading Serial Assistant Software**

You can download the serial assistant software by yourself according to your personal preference. There is no need for a specific tool. The following uses Mobaxterm as an example.

Mobaxterm software download address: https://mobaxterm.mobatek.net/

**Mobaxterm Operation Steps**

1. Open Mobaxterm and click the "Session" button to create a new session window.
2. In the session type list, select "Serial".
3. Select the serial port COM number, which can be viewed by opening the Windows Device Manager -> Ports interface.
4. Set the serial port baud rate to 1500000.
5. Click "OK" to start the session window.

![image-20250418175523261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418175523261.png)

## ADB

The K8 Android/Debian system supports the use of ADB. For details, refer to the [ADB Tutorial](../../../common/zh/adb/ADB_Tutorial.md).

## SSH

The K8 supports the use of SSH. For details, refer to the [SSH](../../../common/zh/linux/SSH.md).

## Common Issues

### Power Supply Issues

When the motherboard is connected to multiple peripherals, resulting in high power consumption, the power-on green light may be dim or not stay lit after power-on, and the system may also experience abnormal startup.

**Solution**: This is generally a power supply issue. Please check whether the power supply has sufficient power to support the connected peripheral modules.

### MIPI Screen Display Issues

If the MIPI screen fails to display, check the wiring and whether the backlight is lit properly.

**Solution**:

If the backlight is not lit, check the wiring. [MIPI_DSI Wiring](#MIPIDSI)
If the backlight is lit, check the software. (K7 V1.2 does not support automatic recognition of MIPI screens yet. Different screens require different software to be flashed.)

### Debug Serial Port Usage Issues

When using Mobaxterm or other serial port tools to connect to the serial port, you may encounter issues such as garbled characters or being able to only display but not write.

**Solution**: This is generally because the debug serial port tool does not support a baud rate of 1500000. It is recommended to use a debug serial port tool that supports a baud rate of 1500000.

### Username and Password Errors

When logging in via ssh, you may enter the wrong password.

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

The command-line console, as referred to in this document, generally means an operating interface that allows users to interact with the computer system through text commands. Users can perform operations (such as file management, program execution, system configuration, etc.) by entering specific instructions, rather than relying on graphical user interface (GUI) operations such as mouse clicks and visual interactions.

Examples of command-line consoles:

For the Android system, you can use the serial port or adb shell to access the command-line console.

```
console$
```

For the Debian system, you can use the serial port, adb shell, ssh, etc. to access the command-line console.

```
root@linaro$
```

For the Ubuntu system, you can use the serial port, adb shell, ssh, etc. to access the command-line console.

```
kickpi@kickpi$
```