# 03-Functional_Testing



## Instructions

Unless otherwise specified, the testing methods are applicable to both Ubuntu and Android systems.



## Ethernet

Connect the board's network port to a network cable.

**K2B: 100M/1000M network port**

![image-20250427144337458](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427144337458.png)

The left network port light being on indicates a 100M connection, and the right one being on indicates a 1000M connection.

**K2C: 100M network port**

K2C has no network port lights.

You can view the board's IP address through the router background or the serial port terminal.

![image-20250412164019949](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250412164019949.png)



**User-layer LED Status Control**

The default LED trigger mode is "heartbeat", in which the LED status cannot be manually controlled. To enable command-based LED status control, use the following commands to set the trigger mode to "none" and then adjust the brightness:

```shell
echo none > /sys/class/leds/sys_led/trigger
echo 1 > /sys/class/leds/sys_led/brightness
echo 0 > /sys/class/leds/sys_led/brightness
```

## IR Infrared

**Android**

Use a remote control to operate the main interface.

**Ubuntu**

``` shell
cat /proc/bus/input/devices
hexdump /dev/input/event0
Press the remote control and check for event reports.
```

![image-20250412151436282](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250412151436282.png)

## HDMI

**Android System**

Connect the motherboard to an HDMI-1080P monitor. After the system starts successfully, the interface is as follows:

![image-20240306171855522](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171855522.png)

**Ubuntu System**

Connect the board to an HDMI-1080P monitor. The system display is as follows when booting:

![image-20250412150306655](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250412150306655.png)



## Audio

**Android**

Headphone audio output is disabled by default. Enable analog audio output in Android 12.0 system settings:

![image-20250428151212987](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428151212987.png)

![image-20250428151241109](C:\Users\10539\AppData\Roaming\Typora\typora-user-images\image-20250428151241109.png)

![image-20250428151317031](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428151317031.png)

![image-20250428151339351](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428151339351.png)

![image-20250428151524271](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428151524271.png)

**Ubuntu**

Desktop Playback

Requires an HDMI screen with built-in speakers.

``` shell
cd /bin
sudo ln -s pavucontrol pavucontrol-qt
sudo apt-get update
sudo apt purge pulseaudio
sudo apt install pulseaudio
reboot
```

Select the second option for HDMI output and the first for headphone output in the volume control menu at the top right corner.

![image-20250427194148974](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427194148974.png)

Command-line Playback

``` shell
$ aplay <filepath> 		 # Play audio, use -h for help
$ amixer 				 # Alsa tool for audio configuration, use -h for help
$ cat /proc/asound/cards  # List sound cards
```

K2B

``` shell
HDMI output:
aplay -D hw:0,0 music.wav
```

``` shell
Headphone output:
aplay -D hw:1,0 music.wav
```

K2C

``` shell
HDMI output:
aplay -D hw:2,0 music.wav
```

``` shell
Headphone/speaker output:
aplay -D hw:1,0 music.wav
```



## WiFi / Bluetooth

**Android**

![image-20250428161839792](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428161839792.png)

**Ubuntu**

WiFi

```shell
# View network device list
sudo nmcli dev

# Enable WiFi
sudo nmcli r wifi on

# Disable WiFi
sudo nmcli r wifi off

# Scan nearby hotspots
sudo nmcli dev wifi

# Connect to a hotspot (replace SSID and PASSWORD)
sudo nmcli dev wifi connect "SSID" password "PASSWORD" ifname wlan0
```



**Bluetooth**

1.Install Bluetooth Tools

```shell
$ sudo apt-get update
$ sudo apt-get install bluez
```

2.Check Bluetooth Devices

```shell
$ hciconfig -a  
```

3.Classic Bluetooth Operations

```shell
# Start scanning
bluetoothctl scan on
# Pair with a device (replace with target MAC)
bluetoothctl pair 28:52:E0:19:17:69
# Connect to a paired device
bluetoothctl connect 28:52:E0:19:17:69
# Trust the device for auto-connection
bluetoothctl trust 28:52:E0:19:17:69
# Disconnect
bluetoothctl disconnect 28:52:E0:19:17:69
# Unpair a device (replace with MAC)
bluetoothctl remove FC:E8:06:E7:96:AF   
# Block connection from a device
bluetoothctl block  FC:E8:06:E7:96:AF 
# List paired devices
bluetoothctl paired-devices
```

render audio via bluetooth audio device

``` shell
# Check connected devices
bluetoothctl paired-devices 
# Set Bluetooth as default audio sink (replace MAC)
pacmd set-default-sink bluez_sink.28_52_E0_19_17_69.a2dp_sink
# Play audio through Bluetooth
aplay sample-15s.wav
```

![image-20250428144858777](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428144858777.png)

``` shell
BLE (Low Energy) Scan
$ hcitool -i hci0 cmd 0x03 0x0003
$ hcitool -i hci0 cmd 08 01 3f 00 00 00 00 00 00 00
$ hcitool lescan
```



## USB

Insert a USB flash drive and:

``` shell
lsblk
```

![image-20250412150828553](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250412150828553.png)

mount

``` shell
 mkdir /mnt/sda
 mount /dev/sda1 /mnt/sda/
 ls /mnt/sda/
```

## 4G

**Ubuntu**

> Applicable to K2C

1.Check USB Module Loading

```shell
# lsusb
```

2.Verify Serial Port Creation

```shell
# ls /dev/ttyUSB*
```

3.Re-dial with SIM Card

```shell
# sudo pppd call quectel-ppp &
```

If dialing fails:
``` 
ps -ef | grep pppd  # Find the process ID
kill -9 <PID>       # Terminate the process
# Repeat the dialing command
```



4.External Network Ping Test

```shell
# ping www.baidu.com
```

DNS Resolution Issue?
Edit the resolv.conf file:

```shell
# vim /etc/resolv.conf
```

Add the following DNS servers:

```shell
nameserver 8.8.8.8
nameserver 8.8.4.4
```



**Android**

Android TV system does not support 4G functionality.



## KEY

**Android**

Pressing the USER button wakes the device from sleep; long-pressing shuts it down (long-press does not power it on).

**Ubuntu**

Pressing the USER button opens the "Log out" interface.

![image-20250512100543793](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512100543793.png)

## RS485/232

> Applicable to K2C

KICKPI-K2C supports optional configurations: one RS485 port (/dev/ttyAS3) and one RS232 port (/dev/ttyAS4). Port labels are silkscreened on the board's back.

![image-20250428162920707](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428162920707.png)

Configuration Commands

```shell
stty -F /dev/ttyAS3 115200  # Set baud rate (e.g., 115200 for RS485)
echo "123" > /dev/ttyAS3     # Send data through RS485
cat /dev/ttyAS3              # Receive data from RS485
```



## FAQ

Problem: Unable to access the internet when using both a wired LAN and WiFi connection.

![image-20250427165431777](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427165431777.png)

[Solution: Refer to Resolving Network Priority Issues When Using Wired and Wireless Networks Simultaneously in Linux.](https://blog.csdn.net/qq547276542/article/details/78713893)

