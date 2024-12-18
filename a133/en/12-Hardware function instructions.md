# 12-Hardware function instructions

[TOC]



## Expansion pins

The development board is equipped with a 20Pin expansion pin, which includes ordinary GPIO, I2C, UART, SPI, and other interfaces.

![image-20241009150239258](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241009150239258.png)



* With * as the default software default configuration function
* UART0_RX/TX default software for debugging serial port
* TWI equivalent I2C，TWI1_SCK/TWI1_SDA equivalent I2C_CLK/I2C_SDA



## LRADC

Location: Development board 20Pin [Extension pin](Expansion pins) Foot 1

* Low Rate ADC (LRADC)
* 6-Bit resolution
* Sampling rate up to 2 kHz
* Support for hold keys and universal keys
* Supports normal, continuous, and single working modes
* Power supply voltage: 1.8 V. Power reference voltage: 1.35 V. Analog input. Detection voltage range: 0 to LEVELB (the maximum value is 1.266 V).



## LED

The development board is equipped with two LED lights. By default, the green LED is the power indicator and the blue LED is the heartbeat indicator.

The power indicator light is always on when powered on, and the heartbeat light core continues to flash after starting. In addition, you can refer to the following aspects to control LED as other functions.

By observing the status of two LEDs, the operating status of the development board can be preliminarily judged.

If the power indicator does not light up, check the power supply part for abnormality. If the heartbeat indicator does not flash, the system operates abnormally.




### User layer control LED status

The default LED trigger mode is heartbeat, and the LED state cannot be controlled by humans in this mode.

If you want to control the LED state by command, you can set it to none by the following command, and then set the brightness of the LED

```
$ echo none > /sys/class/leds/work-led/trigger
$ echo 1 > /sys/class/leds/work-led/brightness
$ echo 0 > /sys/class/leds/work-led/brightness
```



## GPIO

There are 9 controllable GPIOs by default in the extended 20-pin pins of the K2B development board.

First, confirm the GPIO pin position and number through the [Extended Pins](Extended Pins) section, and refer to the following steps to control the state of the specified GPIO.



**View the GPIO registration list**

```
$ ls /sys/class/leds/
	PC7  PC12  PI6  sys_led
```



**Command line controls GPIO level**

```
$ echo 1 > /sys/class/leds/sys_led/brightness
$ echo 0 > /sys/class/leds/sys_led/brightness
```



**Read GPIO level**

```
$ cat /sys/class/leds/sys_led/brightness
```

> 0 is low
>
> 255 is high



**For example, control the PH8 pin**

```shell
console:/ # ls /sys/class/leds/
PH8 fan_en spk_en sysled
console:/ # ls /sys/class/leds/PH8
brightness device max_brightness power subsystem trigger uevent
console:/ #
console:/ # cat /sys/class/leds/PH8/brightness
255
console:/ #
console:/ # echo 0 > /sys/class/leds/PH8/brightness
console:/ #
console:/ # cat /sys/class/leds/PH8/brightness
0
console:/ # echo 1 > /sys/class/leds/PH8/brightness
console:/ # cat /sys/class/leds/PH8/brightness
1
console:/ #
```



## Debug serial port

**Debug serial port wiring of the development board**

The debugging serial port is embedded on the extension pin. You can view the debugging serial port location through the extension pin chapter or the figure below.

Connection method:

​		Main board RX - > Serial Tool TX

​		Main board TX - > Serial Tool RX

![uart](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/uart.jpg)



**Download serial port assistant software.**

Serial port assistant software can be downloaded by yourself. The following takes Mobaxterm as an example.

Mobaxterm software download address:https://mobaxterm.mobatek.net/



**Mobaxterm action steps**

1. Create a new conversation window.

2. Select the session window type as serial.

3. Select the serial port COM number (open the Device Manager of the Windows system -> the port interface to view the COM number).

4. Select serial port baud rate 115200

5. Start session window

![image-20240319182217644](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240319182217644.png)



## ordinary serial port

**After registering the node**

```
$ ls dev/tty*
```



## WIFI / BlueTooth

K5 development board with AW859A high-performance WIFI module

Support 2.4G/5G dual-band, WIFI5, 1T1R, BT5.0




## KEY

K2B is equipped with 3 buttons, namely FEL KEY, RST KEY, PER KEY

FEL KEY：The button can be used to enter burn mode during the UBOOT phase

RESET KEY : System Reset Key

POWER KEY : Power button



## RTC

The silk screen on the main board is RTC, and the test RTC function must be connected to an external button battery, and confirm that the battery is sufficient

**Power-down save time test**

Test steps:

1 The development board is connected to the network and automatically synchronizes the correct network time

2 Disconnect the network connection and disconnect the power supply of the development board

3 After waiting for a few hours, reconnect the development board power supply

4 If the system time is the same as the current time, the RTC function is normal



## LCD

K5 development board supports Single LVDS, DUAL LVDS



### Single LVDS

Screen printing on the main board is LVDS_LCD, 30 PIN clip-on seat



### DUAL LVDS

DUAL LVDS power supply voltage can be selected from 3.3V, 5V, 12V. The corresponding voltage must be selected according to the specification, otherwise it will not light up or burn the screen.

DUAL_LVDS, 30 PIN upright plug-in seat on main board



Note: According to the different screen power consumption, the power adapter needs to provide enough current, otherwise there will be screen backlight flickering!

If the backlight flickers, please power off in time and replace the adapter.



## FAN
Screen printing on the main board is FAN

The FAN on the silk screen controls the fan through GPIO, and the software is turned on by default

The control method is as follows

```shell
// 查看风扇状态
console:/ # cat /sys/class/leds/fan_en/brightness
255
// 打开
console:/ # echo 255 > /sys/class/leds/fan_en/brightness
// 关闭
console:/ # echo 0 > /sys/class/leds/fan_en/brightness
```



## 4G communication module




## Speaker
The main board is silk-screened with P and N logos, and there are two seats. The speaker seat on the side near the fan is the left channel, and the other is the right channel



## earphones
The silk screen on the main board is HEADPHONE

3.5 mm headphone jack, only supports audio output function

## ETH

Support Gigabit Network

```
test rate:
uplink rate 783 Mbits/sec 
downlink rate 898 Mbits/sec 
```



## tinycap Recording

```
console:/ # tinycap
Usage: tinycap file.wav [-D card] [-d device] [-c channels] [-r rate] [-b bits] [-p period_size] [-n n_periods] [-T capture time]
```



```
tinycap file.wav -D 0 -d 0  -c 2 -r 48000 -b 16 -T 10
```





## Linux Functional Testing

### alsa tool environment

```
sudo apt install alsa-utils
```

```
sudo apt-get install sox libsox-fmt-all
```

list control lists

```
root@kickpi:~# amixer controls
numid=14,iface=MIXER,name='Headphone Switch'
numid=2,iface=MIXER,name='ADC Swap'
numid=8,iface=MIXER,name='ADC volume'
numid=12,iface=MIXER,name='ADCL Input MIC1 Boost Switch'
numid=13,iface=MIXER,name='ADCR Input MIC2 Boost Switch'
numid=7,iface=MIXER,name='DAC volume'
numid=15,iface=MIXER,name='HpSpeaker Switch'
numid=11,iface=MIXER,name='LINEOUT Output Select'
numid=16,iface=MIXER,name='LINEOUT Switch'
numid=6,iface=MIXER,name='LINEOUT volume'
numid=4,iface=MIXER,name='MIC1 gain volume'
numid=5,iface=MIXER,name='MIC2 gain volume'
numid=10,iface=MIXER,name='audiocodec rx_sync switch'
numid=1,iface=MIXER,name='codec hub mode'
numid=3,iface=MIXER,name='digital volume'
numid=9,iface=MIXER,name='headphone gain'
```

use

```
amixer cget + [controls内容]
```



### Turn on the earphone switch.

Check the earphone switch status.

```
# amixer cget numid=14,iface=MIXER,name='Headphone Switch'
numid=14,iface=MIXER,name='Headphone Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=off
```

Turn on the earphones.

```
# amixer cset numid=14,iface=MIXER,name='Headphone Switch' 1
numid=14,iface=MIXER,name='Headphone Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
```



### Microphone switch

```
numid=12,iface=MIXER,name='ADCL Input MIC1 Boost Switch'
open
amixer cset numid=12,iface=MIXER,name='ADCL Input MIC1 Boost Switch' 1
amixer cset numid=13,iface=MIXER,name='ADCR Input MIC2 Boost Switch' 1
close
amixer cset numid=12,iface=MIXER,name='ADCL Input MIC1 Boost Switch' 0
amixer cset numid=13,iface=MIXER,name='ADCR Input MIC2 Boost Switch' 0
view
amixer cget numid=12,iface=MIXER,name='ADCL Input MIC1 Boost Switch'
amixer cget numid=13,iface=MIXER,name='ADCR Input MIC2 Boost Switch'
```



### recording test

```
arecord test.wav
arecord -r 16000 -f S16_LE -d 5 test.wav
arecord -r 48000 -f S16_LE -d 5 test.wav -c 2
```

```
-r representative rate
Currently testing support
	8000
	16000
	24000
	32000
	48000
-c 
	1: Mono
	2: Stereo
-d 
	S16_LE
	S24_LE
```



### play test

```
aplay test.wav
play test/Music/1.mp3
```



### Speaker control

 (The audio stream has the same output as the earphone, so the earphone switch must be turned on at the same time)

```
open
echo 255 > /sys/class/leds/spk_en/brightness

close
echo 0 > /sys/class/leds/spk_en/brightness
```



### Fan switch

The software defaults to on

```
打开
echo 255 > /sys/class/leds/fan_en/brightness
关闭
echo 0 > /sys/class/leds/fan_en/brightness
```



### WiFi test

```
View the list of network devices
sudo nmcli dev

Turn on wifi.
sudo nmcli r wifi on

Turn off wifi
sudo nmcli r wifi off


Scan attachment hotspot
sudo nmcli dev wifi

Connect to the PASSWORD hotspot of the user's SSID password (other hotspots modify the corresponding user and password)
sudo nmcli dev wifi connect "SSID" password "PASSWORD" ifname wlan0
```



### Bluetooth test

```
sudo apt-get install bluez
hciconfig -a
hcitool scan
```



### 4G module testing

Precautions

```
Confirm whether the usb module is loaded
lsusb

Confirm whether the serial port node is generated
ls /dev/ttyUSB*

Insert the calling card, it is recommended to restart.
Pay attention to the EC20 module antenna and the testing environment
```

dial-up test

```
pppd call quectel-ppp &

ping www.baidu.com //test

There may be no broadcast.

Kill by switching the background program
jobs   // Check the background
fg %1 
ctrl + c 
Redial pppd call quectel-ppp &

Or check the process number through ps -ef | grep pppd
kill -9 (Process ID)
Redial pppd call quectel-ppp &
```



### SD card, test

```
$ fdisk -l
Device         Boot Start       End   Sectors  Size Id Type
/dev/mmcblk1p1      32768 124735487 124702720 59.5G  7 HPFS/NTFS/exFAT

$ mount /dev/mmcblk1p1 /mnt/
```



### Touch test

```
cat /proc/bus/input/devices
apt install evtest
evtest
```



### USB flash drive, SD card

```
fdisk -l
```



## USB camera

> Test Platform Android

It can be used by connecting the USB camera directly, but there are compatibility issues in the current test.

During the test, the 1080P test was normal, and the 720P had preview deformation and could not record.



