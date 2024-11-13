# 04-Hardware Factory Test



## 1. Pin Expansion

The development board is equipped with a 20-pin expansion interface, which includes GPIO, I2C, UART, SPI, and other interfaces.

![image-20240319154848492](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240319154848492.png)

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

![image-20240319182217644](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240319182217644.png![1731493336424](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1731493336424.png)

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

