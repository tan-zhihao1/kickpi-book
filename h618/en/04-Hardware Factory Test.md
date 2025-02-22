# 04-Hardware Factory Test



## 1. Pin Expansion

The development board is equipped with a 20-pin expansion interface, which includes GPIO, I2C, UART, SPI, and other interfaces.

![image-20240319154848492](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240319154848492.png)



### K2C 20PIN Pin Diagram

The development board is equipped with a 20Pin expansion pin, which includes ordinary GPIO, I2C, UART, SPI, and other interfaces.

![image-20250222151012102](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222151012102.png)

With * as the default configuration function of the output software
UART0_RX /TX default configuration serial debugging
TWI or I2C: TWI1_SCK/TWI1_SDA I2C_CLK/I2C_SDA

### 1.1. GPIO

The K2B development board's 20-pin expansion interface includes 9 controllable GPIO pins by default.

First, confirm the GPIO pin positions and numbers through the [[Pin Expansion]] section. Refer to the following steps to control the specified GPIO pin status.

**View GPIO registration list**

```
bashCopy code$ ls /sys/class/leds/

gpio1b0/ gpio1b2/ gpio1d4/ gpio3b6/ 
gpio1a4/ gpio1b1/ gpio1d0/ gpio3b5/ gpio4c4/
```

**Control GPIO via Command Line**

```
bashCopy code$ echo 1 > /sys/class/leds/gpio3b6/brightness
$ echo 0 > /sys/class/leds/gpio3b6/brightness
```



### 1.2. Debug Serial Port Usage

**Development Board Debug Serial Port Wiring**

The debug serial port is embedded in the expansion interface and can be viewed through the [[Pin Expansion]] section or the image below:

![image-20240319154747393](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240319154747393.png)

**Download Serial Port Assistant Software**

You can download serial port assistant software on your own. Below, we take Mobaxterm as an example.

Download Mobaxterm software from: https://mobaxterm.mobatek.net/

**Mobaxterm Operation Steps**

1. Create a new session window.
2. Select session window type as serial.
3. Choose the COM port number (Open the Windows Device Manager -> Ports to view the COM port number).
4. Select the serial port baud rate as 115200.
5. Start the session window.

![1731493336424](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1731493336424.png)

## 2. LED

The development board is equipped with two LEDs. By default, the green LED indicates power status, and the blue LED indicates heartbeat status.

The power indicator LED remains lit when powered on, and the heartbeat LED blinks continuously after the kernel starts. Additionally, the LED can be controlled for other purposes as follows.

The status of the two LEDs can be used to preliminarily determine the operating status of the development board.

If the power indicator LED is not lit, check the power supply for abnormalities. If the heartbeat LED does not blink, the system may be running abnormally.



### User-Level LED State Control

By default, LED triggering is set to "heartbeat," and under this mode, LED states cannot be manually controlled.

To control the LED state with commands, you can set the trigger to "none" using the following commands, and then set the brightness of the LED:

```
bashCopy code$ echo none > /sys/class/leds/work-led/trigger
$ echo 1 > /sys/class/leds/work-led/brightness
$ echo 0 > /sys/class/leds/work-led/brightness
```



## 4. WIFI / BlueTooth

The K2B development board is equipped with the AW859A high-performance WIFI module.

It supports 2.4G/5G dual-frequency band, WIFI5, 1T1R, and BT5.0.



## 5. KEY

The K2B is equipped with 3 buttons: RECOVERY KEY, RST KEY, MASKROM KEY.

The RECOVERY button can be used to enter the burning mode during the UBOOT stage and can be used as a user-defined function button after entering the system.



## 6. Headphone

### Audio Playback Test

By default, the headphone audio output is disabled. Enable analog audio output in the Android 12.0 system settings:

![image-20240301133912142](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240301133912142.png)

![image-20240301133940718](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240301133940718.png)

![image-20240301134005013](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240301134005013.png)

![image-20240301134029929](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240301134029929.png)

![image-20240301134102184](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240301134102184.png)

## 9.RS485/232

> Version: K2C

KICKPI-K2C design is optional: 1 channel 485 (/dev/ttyAS3) 1 channel 232 (/dev/ttyAS4)

![1735098505539](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1735098505539.jpg)

Verify with microcom:

The received content will be printed, but the sent content will not be printed.

```shell
# microcom -s 115200 /dev/ttyAS3
```

Verify with minicom:

The received content will be printed, but the sent content will not be printed.

```shell
# minicom -b 9600 -D /dev/ttyAS3
```

> CRT + A enter control B-Z command Z for help

> | Ctrl+A X | exit the program                                  |
> | -------- | ------------------------------------------------- |
> | Ctrl+A W | Enable/disable line wrapping, disabled by default |
> | Ctrl+A E | Enable/Disable Input Display, disabled by default |
> | Ctrl+A C | clear screen                                      |

If the serial port is not properly occupied after exiting: minicom -s turns off the Serial port setup > > F Hardware Flow Control

Or use echo to send directly.

```shell
# stty -F /dev/ttyAS3 -a 	//You can see the default baud rate.
# stty -F /dev/ttyAS3 115200 	//Can be set
# echo "123" > /dev/ttyAS3
```



## 10.4G verification

> Version: K2C

1.Confirm whether the usb module is loaded

```shell
# lsusb
```

2.Confirm whether the serial port node is generated

```shell
# ls /dev/ttyUSB*
```

3.Plug and unplug the calling card and re-dial.

```shell
# sudo pppd call quectel-ppp &
```

There may be no broadcast.
View process number by'`ps -ef | grep pppd` '
'`Kill -9 (process number)` 'to end the process
And then dial again.

4.Extranet ping test

```shell
# ping www.baidu.com
```

Ping extranet may fail to resolve DNS, you need to add the corresponding DNS.

Modify the resolv.conf file

```shell
# vim /etc/resolv.conf
```

The following can be added:

```shell
nameserver 8.8.8.8
nameserver 8.8.4.4
```

