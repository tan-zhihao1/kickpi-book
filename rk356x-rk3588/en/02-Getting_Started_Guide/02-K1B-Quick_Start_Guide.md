# 02-K1B-Quick_Start_Guide

This guide aims to assist users in quickly getting started with the motherboard, covering topics such as powering on the board, connecting the display, using the debug serial port, ADB, and SSH connections.

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the motherboard's command-line console. [Motherboard Command-Line Console](#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

## Powering on the Board

Currently, all series of boards are set to boot automatically upon power-on by default. The following introduces the power supply methods for each board and the signs indicating the normal startup of the board.

### Power Supply

The K1B board operates at a supply voltage of 5V and supports two power supply methods: TYPE-C and expansion pins.

* TYPE-C Port (Recommended)

![image-20250417191511204](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250417191511204.png)

* Expansion Pin Power Supply

In special cases where the TYPE-C port cannot supply power to the board, you can use the 1st 5V pin in the expansion pins, paired with the 6th GND pin for power supply.

With this power supply method, the maximum supply current is approximately **0.5A**.

![image-20250417191801337](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250417191801337.png)

Connect an external 5V power supply to **Pin 1 (VCC)** and **Pin 6 (GND)** of the 20-pin expansion header.

![image-20250421103741365](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250421103741365.png)

### LED Indicators

The development board is equipped with two LEDs:

The green LED serves as the power indicator, which remains lit by default when powered on.
The blue LED acts as the system heartbeat indicator. If the heartbeat LED keeps blinking, it indicates that the kernel is running normally.
Additionally, the system heartbeat LED can be controlled for other functions as described below.

### Power-On Steps

1. Plug in the DC12V power supply to  board..
2. checking the status of the two LEDs:
   - The power indicator should remain lit as soon as power is applied. If it does not light up, check the power supply section for any abnormalities.
   - The heartbeat LED should keep blinking after the kernel starts. If not, the system may be running abnormally.

## Display

The K1B motherboard has display interfaces. You can check the number of interfaces and the types of default compatible displays in the [K1B Hardware Introduction](../01-Development_board_Introduction/03-KICKPI-K1B_Hardware_Introduction.md#display) section. You can connect display peripherals as needed.

**All display interfaces on the KICKPI development board are used with corresponding display adapter boards. The purchased displays will be shipped with the adapter boards already connected.**

### LVDS/MIPI

The K1B is default compatible with LVDS 7-inch screens, MIPI 5-inch, 8-inch, and 10.1-inch screens sold by KICKPI.

LVDS/MIPI 0 Interface: [Flip-down FPC Interface](../../../common/en/display/FPC_Interface_Introduction.md#FPC-1)

![image-20250512140834526](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512140834526.png)

### HDMI

The motherboard is equipped with an HDMI OUT display interface. The interface type is HDMI Type-A and supports hot-plugging.

![image-20250512141342753](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512141342753.png)

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

The debug serial port is a UART port on the motherboard, mainly used during the development and debugging phases to help developers understand the internal working status of the system. The debug serial port is sometimes also referred to as the console port or debug terminal.

**Baud Rate: 1500000**

> The USB-to-UART cable connected to the socket also needs to support a baud rate of 1500000 for normal communication.

**Debug Serial Cable Description**

The currently provided debug serial cable has four wires. The red wire is `VCC`, the green wire is `TX`, the white wire is `RX`, and the black wire is `GND`. Generally, there is no need to connect `VCC`.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

> The same applies to other debug tool cables. Only connect TX, RX, and GND.

**Viewing the DEBUG Pins**

Each model of the motherboard is equipped with a DEBUG serial port, which is located among the 20-pin/40-pin headers. You can check the location of the DEBUG port in the **Expansion Pins** section of the hardware introduction for each model of the motherboard in the motherboard introduction. Additionally, the location is marked with silk screen on the front and back of the board.

![image-20250422094320253](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422094320253.png)

**Connecting the Debug Serial Port of the Development Board**

Connect the `TX` of the debug cable to the `RX` of the motherboard, the `RX` of the debug cable to the `TX` of the motherboard, and the `GND` of the debug cable to the `GND` of the motherboard. There is no need to connect `VCC`.

| Motherboard Expansion Pins | Debug Cable Pins |
| :------------ | ---------- |
| UART_RX_DEBUG | TX |
| UART_TX_DEBUG | RX |
| GND | GND |

**Downloading the Serial Assistant Software**

There are many free/commercial serial assistant tools, no need to use a special one, you can choose the one you preferred. Here we use Mobaxterm as an example.

Mobaxterm software download address: https://mobaxterm.mobatek.net/

**Mobaxterm Operation Steps**

1. Open Mobaxterm and click the "Session" button to create a new session window.
2. In the session type list, select "Serial".
3. Select the serial port COM index, which can be retrieved by checking the Windows Device Manager -> COM and LPT.
4. Set the serial port baud rate to 1500000.
5. Click "OK" to start the session window.

![image-20250418175523261](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250418175523261.png)

## ADB

The K1B Android/Debian system supports the use of ADB. For details, refer to the  [ADB Tutorial](../../../common/zh/adb/ADB_Tutorial.md).

## SSH

The K1B supports the use of SSH. For details, refer to [SSH](../../../common/zh/linux/SSH.md).

## Common Issues

### Power Supply Issues

When the motherboard is connected to multiple peripherals, resulting in high power consumption, after connecting the power supply, the power-on green light may be dim or not stay lit constantly, and the system startup may also encounter issues.

**Solution**: This is generally a power supply issue. Check whether the power supply can provide sufficient power to support the connected peripheral modules.

### MIPI Screen Display Issues

If the MIPI screen fails to display, check the wiring and whether the backlight is lit properly.

**Solution**:

If the backlight is not lit, check the wiring. [MIPI_DSI Wiring](#MIPIDSI)
If the backlight is lit, check the software. (K7 V1.2 does not support automatic MIPI screen recognition yet. Different screens require different software to be flashed.)

### Debug Serial Port Usage Issues

When using Mobaxterm or other serial port tools to connect to the serial port, you may encounter issues such as garbled characters or being able to only view but not input data.

**Solution**: This is generally because the debug serial port tool does not support a baud rate of 1500000. It is recommended to use a debug serial port tool that supports a baud rate of 1500000.

### Username and Password Errors

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

The command-line console, as referred to in this document, generally means an interface where users can interact with the computer system through text commands. Users execute operations (such as file management, program execution, system configuration, etc.) by entering specific instructions, rather than relying on graphical user interface (GUI) operations like mouse clicks.

Examples of command-line consoles:

For the Android system, you can use the serial port or adb shell to access the command-line console.

```
console$
```

For the Debian system, you can use the serial port, adb shell, ssh, etc., to access the command-line console.

```
root@linaro$
```

For the Ubuntu system, you can use the serial port, adb shell, ssh, etc., to access the command-line console.

```
kickpi@kickpi$
```