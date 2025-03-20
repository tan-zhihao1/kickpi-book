# 12-Hardware function instructions



## 1. ExpansionPin<a id="ExpansionPin"> </a>

Development board expansion pin capabilities

* Can be used to connect external devices (temperature sensors, humidity sensors, etc.)
* Communicate with other boards or modules (I2C, SPI, UART, etc.)
* Customize GPIO functions

See the pin list for specific function expansion details.

The list of pins is marked with the default configuration of each pin (marked with an *), and an optional configuration corresponding to that pin is given.



### K1 pin list

![image-20250222170528749](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222170528749.png)



### K1B pin list 

![image-20250222170554561](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222170554561.png)



### K3 pin list 

![image-20250222170607362](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222170607362.png)

### K8 pin list 



## 2. LED<a id="LED"> </a>

The development board is equipped with 2 LEDs, the green LED is the power indicator, and it is always on by default when powered on.

The blue LED is the system working heartbeat light. If the heartbeat light continues to flash, it indicates that the core is running normally.

In addition, the system working heartbeat light can refer to the following aspects to control LED as other functions.



User layer control LED status

**Command line control LED status**

The default LED trigger mode is heartbeat, and the LED state cannot be controlled by humans in this mode.

If you want to control the LED state by command, you can set it to none by the following command, and then set the brightness of the LED

```bash
$ echo none > /sys/class/leds/work-led/trigger
$ echo 1 > /sys/class/leds/work-led/brightness
$ echo 0 > /sys/class/leds/work-led/brightness
```

Restore heartbeat mode

```
$ echo heartbeat > /sys/class/leds/work-led/trigger
```



## 3. GPIO

Multi-channel controllable GPIO is configured by default in the expansion pins of the development board.

First, through the [Expansion Pin] (#ExpansionPin) section, confirm the GPIO pin position and number, and refer to the following steps to control the state of the specified GPIO.



### The user layer controls the GPIO level

> Default software extension pins configured as GPIO are generally registered for this mode of control
>
>This method can control the pin output high or low



**View GPIO registration list**

```
$ ls /sys/class/leds/

gpio1b0/ gpio1b2/ gpio1d4/ gpio3b6/ 
gpio1a4/ gpio1b1/ gpio1d0/ gpio3b5/ gpio4c4/
```



Command Line Control GPIO

```
$ echo 1 > /sys/class/leds/gpio3b6/brightness
$ echo 0 > /sys/class/leds/gpio3b6/brightness
```



### sys gpio control

When there is a need to configure the extension pin as an input, the default software's GPIO-LED does not meet the requirement.

You need to modify the software and re-burn it. Details



## 4. UART

### Debug serial port use

**View DEBUG pin**

The Kickpi development board debugging serial port is generally embedded in the extension pin, and the corresponding DEBUG pin position can be viewed through the [Extension Pin] (#ExpansionPin) chapter, which is generally arranged in order as UART_RX_DEBUG, UART_TX_DEBUG, and GND.



**Debugging serial cable instructions**

The debugging serial cable currently provided has four wires, the red one is'VCC ', the green one is'TX', the white one is'RX ', and the black one is'GND'. Generally, it is not necessary to connect to'VCC '.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

> The same is true for other debugging Creative Tools, just connect TX RX GND



**Development board debugging serial port wiring**

* Debugging serial port location wiring instructions

The debugging line "TX" is connected to "RX" on the main board, "RX" is connected to "TX" on the main board, and "GND" is connected to "GND" on the main board. There is no need to connect "VCC".

![image-20241231151848065](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231151848065.png)

> PIN on main board：RX	TX	GND
>
> PIN of the debugging line：TX	RX	GND



**Download Serial Port Assistant Software**

Serial assistant software can be downloaded by yourself. The following is an example of Mobaxterm

Mobaxterm software address download: https://mobaxterm.mobatek.net/



**Mobaxterm operation steps**

1. Create a new session window
2. Select the session window type as serial
3. Select the COM number of the serial port (open the Windows System Facility Management - > Port interface to view the COM number)
4. Select the serial port baud rate 1500000
5. Start the session window

![image-20250222170801465](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250222170801465.png)



### Ordinary serial port use

1. Confirm the string slogan used

2. Check if the corresponding node exists

```
$ ls /dev/ttyS*
/dev/ttyS4
```

3. Use microcom for communication testing

```
$ microcom -s 115200 /dev/ttyS4
```

> If the serial port 4 found node is /dev/ttyS4
>
> -S Set baud rate to 115200



## 5. USB

### OTG mode switching

Android 13.0 system USB supports OTG mode by default, and can automatically switch leader/follower functions

Linux system USB defaults to HOST mode, does not support OTG for the time being



### ADB debugging

Android supports adb debugging (type-c interface to connect to the host)

The main commands of **adb** are as follows:

##### adb devices
- Description: To view the currently connected devices, the devices connected to the computer or the emulator will be listed and displayed
- adb start-server
- Description: Start adb

#### adb kill-server
- Description: Close adb

#### adb shell
- Description: This command will log into the shell of the device, followed by the command to run the device directly, which is equivalent to executing a remote command

#### adb push
- Description: This command can copy files from the computer to the development board
-Example: adb push hello.c /tmp/This copies the hello.c file on the computer to the/tmp directory on the development board

#### adb pull
- Description: This command can copy the files on the development board to the computer
- Example: adb pull /tmp/hello.c./Copy the file hello.c in the/tmp directory on the development board to the current directory

#### adb version
- Description: Check the version of adb

#### adb help
- Description: adb help, for more command details, please use the adb help command to view




## 6. PWM<a id="PWM"> </a>

RK3568 has 4 PWM modules, each PWM module has 4 channels of PWM, a total of 16 channels of PWM.

### User layer configuration PWM output

Extend 40Pin interface, including multi-channel PWM, take PWM3 channel as an example, enter the PWM configuration according to the following command

After the configuration parameters are successfully set according to the example, the PWM3 pin can be measured with a multimeter, and the correct voltage should be about 1.6V



Example: Set PWM3 channel, period 10000ns, duty cycle 5000ns, polarity is normal

The PWM path is/sys/class/pwm, and pwmchip* is sorted from 0 in your enable order. Set pwmchip0 as follows:

```
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```



* How to confirm the pwm corresponding to the pwmchip

```
$ find /sys/devices/ | grep pwmchip | grep uevent
/sys/devices/platform/fd8b0020.pwm/pwm/pwmchip1/uevent
/sys/devices/platform/fd8b0010.pwm/pwm/pwmchip0/uevent
/sys/devices/platform/fd8b0030.pwm/pwm/pwmchip2/uevent
```

>fd8b0010.pwm For the node corresponding to dts 



## 7. WIFI

K1 development board with RTL8822CS high-performance WIFI module

Support 2.4G/5G dual band, WIFI5, 2T2R



![image-20231215153316513](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153316513.png)



### Graphical interface configuration WIFI

![image-20240120091252595](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240120091252595.png)



### Command line configuration WIFI hotspot (AP) mode

**Check if AP mode is supported**

```
$ iw list | grep AP
Device supports AP-side u-APSD.
		 * AP
		 * AP/VLAN
		HE Iftypes: AP
		HE Iftypes: AP
		 * wake up on EAP identity request
		 * AP/VLAN
		 * #{ managed } <= 1, #{ AP, P2P-client, P2P-GO } <= 1, #{ P2P-device } <= 1,
	Driver supports full state transitions for AP/GO clients
	Driver/device bandwidth changes during BSS lifetime (AP/GO mode)
		 * AP: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
		 * AP/VLAN: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
		 * AP: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
		 * AP/VLAN: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0

```

> AP/VLAN can indicate hardware support



```
#Create dependencies
sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged 

# Create a virtual network interface card
sudo iw dev <wirelessname> interface add <virtualwlanname> type __ap  
# < WirelessName > is the real wireless network interface card name, which can be viewed through ifconfig, < virtualwlanname > is the virtual wireless network interface card name
#Such as command 
sudo iw dev wlan0 interface add wlo2 type __ap

#Add physical address for virtual network interface card
sudo ip link set dev <virtualwlanname> address 22:33:44:55:66:00
# Feel free to fill in, if there is a conflict, change it, < virtualwlanname > is the virtual wireless network interface card name
#For example, the command:
sudo ip link set dev wlo2 address 22:33:44:55:66:00
```



```
#View creation status
sudo iw dev <virtualwlanname> info
sudo iw dev wlo2 info

# The output is similar
   Interface wlo2
	ifindex 5
	wdev 0x5
	addr 04:e2:b9:17:18:72
	type managed
	wiphy 0
	txpower 0.00 dBm
	multicast TXQ:
		qsz-byt	qsz-pkt	flows	drops	marks	overlmt	hashcol	tx-bytestx-packets
		0	0	0	0	0	0	0	0	0
```

> Note: After restarting the computer, the virtual network interface card created here will be invalid



1. Download the installation tool create_ap

```shell
git clone https://github.com/oblique/create_ap
cd */create_ap
sudo make install
```

2. Use create_ap to create hotspots

```shell
sudo create_ap -c 11 <virtualwlanname> <wirelessname> <SSID> <password> 
```

>  < wirelessname > is the name of your wireless network interface card, < virtualwlanname > virtual network interface card name, < SSID > < password > is the hotspot wifi name and password created respectively 

for example 

```shell
sudo create_ap -c 11 wlo2 wlan0 m3 88888888
```

3. If the created hotspot gets stuck

```
Open Hotspot Times with the following error:
#RTNETLINK answers: Device or resource busy

#ERROR: Maybe your WiFi adapter does not fully support virtual interfaces.
     #  Try again with --no-virt.
```

You can stop the hotspot created before, and then restart the hotspot as follows.
```
sudo create_ap --stop <virtualwlanname>  
```

> < virtualwlanname > virtual network interface card name

## 8. Bluetooth

The K1 development board is equipped with an RTL8822CS high-performance Bluetooth module.



## 9. SATA hard drive

K1 development board, equipped with a SATA3.0 hard disk interface

### Hardware wiring

SATA hard disk wiring is divided into hard disk power supply interface and hard disk data interface

> Special attention: The SATA hard disk power supply interface is a PH2.0 terminal, you need to buy another PH2.0 terminal to SATA power cord.
>
> The power supply part contains 5V and 12V. You must confirm that the wiring sequence is correct before connecting to the hard disk, otherwise the hard disk will be burned out.



Keep the development board powered off, power on after connecting to the hard disk, Android/Linux will automatically mount the hard disk partition

Special attention: try to partition the hard disk in advance, and the partition format



![image-20231215153119268](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153119268.png)





## 10.M.2 SSD

K1 development board, equipped with one PCIE3.0 M.2 SSD hard disk interface

Android system, only supports'NTFS 'or'Fat32' format partitions

It is recommended to format the SSD in advance and create a partition that supports the format
>
> If the SSD partition is not in a format supported by the system, you can also follow the system prompts to format the partition, and the default format is Fat32.



### Hardware wiring

The M.2 interface supports the current mainstream 2280 specification hard disk and is equipped with fixed studs

Make sure that the development board is powered off, and power it on after connecting to the hard disk.

![image-20231215153247719](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153247719.png)

### Performance testing

Test SSD Model: Samsung PM981 256G

Test results:

| Platform | Sequential Write Speed | Sequential Read Speed |
| -------------- | ------------ | ------------ |
| K1 Android13.0 | 243 MB/s | 604 MB/s |
| K1 Debian11 | | |

If there are no special requirements, it is recommended to use Fat32 format partitions



## 11. RTC

The K1 development board is equipped with HYM8563 RTC chip, which can realize the function of saving time after power failure

### Hardware wiring

To test the RTC function, an external button battery must be connected, and confirm that the battery is fully charged.

![image-20231215153345278](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153345278.png)



Power down save time test

Test steps:

1. The development board is connected to the network and automatically synchronizes the correct network time
2. Disconnect the network connection and disconnect the power supply of the development board.
3. After waiting for a few hours, reconnect the development board power supply.
4. If the system time is the same as the current time, the RTC function is normal





## 12. LCD

K1 development board, equipped with three LCD interfaces, namely two LVDS/MIPI interfaces (reusable) and one eDP interface

Three-way LCD interface supports three-screen display/touch

> The default factory firmware is a multi-screen compatible image, which has been adapted to three screens
>
> A firmware can be compatible with any LCD + HDMI output. If you need more than two LCD displays, you need to re-compilation mirroring.



### MIPI screen hardware wiring

**Cable requirements **

The number of pins is 30Pin, and the direction of the cable is the same direction.

> It is recommended to use the cable that comes with the screen, otherwise it may cause damage to the screen.



**Wiring example diagram**

![image-20231215152820685](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215152820685.png)

![image-20231021170724701](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021170724701.png)

> Note: Before plugging in or unplugging any FPC interface peripherals, ensure that the board is completely powered off



### Single LVDS screen hardware wiring

**Cable requirements**

The number of pins is 30Pin, and the direction of the cable is the same direction.

> It is recommended to use the cable that comes with the screen, otherwise it may cause damage to the screen.



**Wiring example diagram**

![image-20231215152838710](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215152838710.png)

![image-20231021171105002](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021171105002.png)

> Note: Before plugging in or unplugging any FPC interface peripherals, ensure that the board is completely powered off



### DUAL LVDS screen hardware wiring

KICKPI K3 supports one Dual LVDS screen, and uses CS5518 chip to realize MIPI to Dual LVDS function

> PS: Dual LVDS screen, and MIPI/LVDS screen, multiplexed with each other, can only choose one of the two



**K3 Dual LVDS Interface Description **

![image-20250225160426210](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250225160426210.png)

> LVDS power supply voltage can be selected from 3.3V, 5V, 12V. The corresponding voltage must be selected according to the specification, otherwise it will not light up or burn the screen.



### eDP screen hardware wiring

**Cable requirements **

The number of pins is 30Pin, and the direction of the cable is the same direction.

> It is recommended to use the cable that comes with the screen, otherwise it may cause damage to the screen.



**Wiring example diagram **

![image-20231021171209133](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021171209133.png)



![image-20231215152730038](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215152730038.png)

> Note: Before plugging in or unplugging any FPC interface peripherals, ensure that the board is completely powered off
>
> EDP screen, an additional 12V power supply must be connected at the screen end





## 13. MIPI CAMERA

The K1 development board supports a single MIPI CSI 4Lane camera interface, which can be connected to up to two cameras

The K3 development board supports dual MIPI CSI 4Lane camera interface, which can be connected to up to four cameras



### Hardware wiring

![image-20231215153036711](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153036711.png)

**Cable requirements **

The number of pins is 40Pin, and the direction of the cable is reversed.

> It is recommended to use the cable that comes with the screen, otherwise it may cause damage to the screen.
>
Before plugging in or unplugging any FPC interface peripherals, ensure that the board is completely powered off



### cheese preview image

```
$ cheese
```

> Command line execution After starting the cheese program, directly display the camera image

![image-20230906140719725](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230906140719725.png)



### Debian gst-launch-1.0 preview image

Debian file system with built-in gst-launch-1.0 test script

```
/rockchip-test/camera/camera_rkisp_test.sh
```

> Depending on the number of connected cameras, it will cause the/dev/video node number to be offset, so that the script cannot open the correct camera.
>
> At this point, you can modify the script to specify the correct video number, device =/dev/video-camera0



## 14. HDMI in

K8 supports one HDMI input. The resolutions supported by EDID in the current driver code include:

```
3840x2160P60、3840x2160P50、3840x2160P30、3840x2160P25、3840x2160P24、
1920x1080P60、1920x1080P50、1920x1080P30、1920x1080i60、1920x1080i50、
1600x900P60、1440x900P60、1280x800P60、
1280x720P60、1280x720P50、1024x768P60、
720x576P50、720x480P60、720x576i50、720x480i60、
800x600P60、640x480P60
```

Supported input formats include:

```
RGB888、YUV420、YUV422、YUV444
```



## 16. GPU

**GPU Usage Rate **

```
$ cat /sys/devices/platform/*gpu/utilisation
```

Move the mouse or window or run a GPU test to see if hard acceleration is working

### Debian GLmark2 performance test

RK Linux file system built-in glmark2-es performance testing tool

Connect the board to the display device, open the virtual end point or debug the serial end point, and execute the following command to start the GPU performance test

```
$ source /rockchip-test/gpu/test_fullscreen_glmark2.sh

#end point print final test score
=======================================================
                                  glmark2 Score: 471
=======================================================
```

> The external display can view the GPU rendering graphics (score about 49), and the debugging serial port can view the GPU rendering log (score about 203).
>
> According to the GPU performance difference, it will take about 10 minutes to complete the performance test.



**K8 test results **

```
$ source /rockchip-test/gpu/test_fullscreen_glmark2.sh
=======================================================
                                  glmark2 Score: 676 
=======================================================
```

> Access HDMI_OUT0, test result is 676



## 17. NPU

RK3568 is equipped with 0.8T computing power NPU unit



**Check NPU usage **

```
$ watch cat /sys/kernel/debug/rknpu/load
$ cat /sys/kernel/debug/rknpu/load
```



### Debian image classification

Open the virtual end point or debug the serial end point, and execute the following command to start the NPU function test

```
$ source /rockchip-test/npu2/npu_stress_test.sh

rknn_api/rknnrt version: 1.4.0 (a10f100eb@2022-09-09T09:07:14), driver version: 0.8.2
model input num: 1, output num: 1
input tensors:
  index=0, name=input, n_dims=4, dims=[1, 224, 224, 3], n_elems=150528, size=150528, fmt=NHWC, type=INT8, qnt_type=AFFINE, zp=0, scale=0.007812
output tensors:
  index=0, name=MobilenetV1/Predictions/Reshape_1, n_dims=2, dims=[1, 1001, 0, 0], n_elems=1001, size=1001, fmt=UNDEFINED, type=INT8, qnt_type=AFFINE, zp=-128, scale=0.003906
custom string: 
Begin perf ...
   0: Elapse Time = 4.95ms, FPS = 201.82
   1: Elapse Time = 4.84ms, FPS = 206.61
   2: Elapse Time = 4.86ms, FPS = 205.85
   3: Elapse Time = 4.79ms, FPS = 208.68
   4: Elapse Time = 4.82ms, FPS = 207.47
   5: Elapse Time = 4.82ms, FPS = 207.25
   6: Elapse Time = 4.81ms, FPS = 207.77
   7: Elapse Time = 4.82ms, FPS = 207.25
   8: Elapse Time = 4.82ms, FPS = 207.34
   9: Elapse Time = 4.82ms, FPS = 207.60
---- Top5 ----
0.984375 - 156
0.007812 - 155
0.003906 - 205
0.000000 - 0
0.000000 - 1
```

The content after "Begin perf..." shows the performance data of the model running multiple times:
>
> - The sequence number of each run is recorded, along with the corresponding elapse time ('Elapse Time') and frame number per second ('FPS'). For example, '0: Elapse Time = 2.85ms, FPS = 351.12' means that the first run of model inference took a total of 2.85 milliseconds, from which 351.12 frames per second can be processed (FPS is calculated by dividing 1000 by the time taken each time, and the unit is converted to get the frame number per second).
>
> `---- Top5 ----` section presents the top 5 probability categories in the model inference output and their corresponding probability values and category numbers:
>
> - '0.935059 - 156' indicates that the model believes that the input data (such as an image) is most likely to belong to the category numbered 156, and its corresponding probability is as high as 0.935059. This probability value is relatively high, indicating that the model has a relatively high confidence level in this judgment.
The following "0.057037-155", "0.003881-205", "0.003119-284", and "0.000172-285" list the category numbers and their probability values from the 2nd to the 5th, respectively. These probability values decrease in turn, indicating that the model's confidence in their belonging to the corresponding category judgment is also gradually decreasing.



**K8 NPU stress testing results **

Access HDMI_OUT0

```
$ source /rockchip-test/npu2/npu_stress_test.sh
...
Begin perf ...
   0: Elapse Time = 2.32ms, FPS = 431.97
   1: Elapse Time = 2.29ms, FPS = 437.64
   2: Elapse Time = 2.22ms, FPS = 450.05
   3: Elapse Time = 2.17ms, FPS = 461.47
   4: Elapse Time = 2.10ms, FPS = 475.51
   5: Elapse Time = 2.15ms, FPS = 464.25
   6: Elapse Time = 2.08ms, FPS = 481.70
   7: Elapse Time = 2.09ms, FPS = 478.24
   8: Elapse Time = 2.09ms, FPS = 479.62
   9: Elapse Time = 2.20ms, FPS = 454.13
---- Top5 ----
0.984375 - 156
0.007812 - 155
...
```

From the print information, it can be seen that an average of 450 frames of data are processed per second



### Debian object detection

Detect data streams, available from cameras or video files

The test results are directly output to the display screen, so you need to connect the display screen to see the results.

```
$ source /rockchip-test/npu2/rknn_yolov5_demo_Linux.sh
```



## 18. MPP

Rockchip MPP provides mpi_enc_test, mpi_dec_test and other testing tools

MPI can be called directly from the command line for hardware codec testing



### hardcoding test

* Encoding H.264 4096x2160 100 frames test

```
$ mpi_enc_test -w 4096 -h 2160 -t 7 -o ./test.h264 -n 100
```

* Encoding H.265 4096x2160 100 frames test

```
$ mpi_enc_test -w 4096 -h 2160 -t 16777220 -o ./test.h265 -n 100
```

Parameter analysis:
>
> mpi_enc_test MPP coding test tool
>
> -W 4096 -h 2160 Specifies the encoded video resolution as 4096x2160
>
> -T 16777220 Specifies the encoding type as H.265
>
> -o./test.h265 Specify the encoded content output file
>
> -N 100 Specify the frame number



### hardware decoder test

* Decode H.264 video 100 frames test

```
$ mpi_dec_test -t 7 -i test.h264 -n 100
```

* Decode H.265 video 100 frames test

```
$ mpi_dec_test -t 16777220 -i test.h265 -n 100
```



**K8 codec test results **

```
Encoding H.264 4096x2160 100 frames test fps 44.65
Encoding H.265 4096x2160 100 frames test fps 45.53
Decode H.264 4096x2160 100 frames test fps 206.22
Decode H.265 4096x2160 100 frames test fps 386.86
```



### chromium video test

Connect the board to the display device, open the virtual end point or debug the serial port end point, and execute the following command to start the chromium video test

```
$ source /rockchip-test/chromium/test_chromium_with_video.sh
```



## 19. ADC

RK3568 with 8-way ADC, K1 development board leads ADC3 channel to expansion pin holder

### Read the ADC3 value

```
$ cat /sys/bus/iio/devices/iio\:device0/in_voltage3_raw
```



## 20. WatchDog

RK3568 is equipped with a guard dog all the way. The door dog is turned on by default when powered on, and the kernel thread automatically feeds the dog.



### User layer configuration Manual dog feeding

The following command can be modified to manually feed the dog at the user level, and the following command must be repeated every 44 seconds to feed the dog, otherwise the system will restart

```
$ echo A > /dev/watchdog
```





## 21. FAN

The power supply voltage of the fan is 5V, and the connection method is shown in the figure below.



![image-20231215152208539](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215152208539.png)



## 22.4G communication module

Currently compatible with Quecte EC20, Quecte EC200M-CN model modules

### Hardware connection method

![image-20231215153217640](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153217640.png)



![1733198014256](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1733198014256.png)

```
/etc/ppp/peers/quectel-pppd.sh /dev/ttyS4
```



![image-20241203115419752](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241203115419752.png)



## 23. Speaker

![image-20231215153423975](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153423975.png)



### Linux play test

**alsa tool **

Linux system can generally control the sound card configuration and audio playback through the alsa tool. At present, Linux system software has built-in related tools.

If there are no relevant tools, you can execute the following command to install

```
$ sudo apt install alsa-utils
```

> After installation, aplay, arecord, amixer and other commands can be used



**Tool Instructions **

```shell
$ amixer -h
Usage: amixer <options> [command]

Available options:
  -h,--help       this help
...

$ aplay -h
Usage: aplay [OPTION]... [FILE]...

-h, --help              help
    --version           print current version
-l, --list-devices      list all soundcards and digital audio devices
...

$ arecord -h
Usage: arecord [OPTION]... [FILE]...

-h, --help              help
    --version           print current version
-l, --list-devices      list all soundcards and digital audio devices
-L, --list-pcms         list device names
...
```

> Use the command -h to view the instructions for the corresponding command



**List to view sound cards **

```
$ cat /proc/asound/cards
 0 [rockchiphdmiin ]: rockchip_hdmiin - rockchip,hdmiin
                      rockchip,hdmiin
 1 [rockchipes8388 ]: rockchip-es8388 - rockchip-es8388
                      rockchip-es8388
 2 [rockchiphdmi0  ]: rockchip-hdmi0 - rockchip-hdmi0
                      rockchip-hdmi0
 3 [rockchiphdmi1  ]: rockchip-hdmi1 - rockchip-hdmi1
                      rockchip-hdmi1
```

> The speaker sound card is es8388, that is, the sound card 1.



**View sound card control configuration list **

```shell
$ amixer -c 1
Simple mixer control 'Headphone',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [off]
Simple mixer control 'Speaker',0
  Capabilities: pswitch pswitch-joined
  Playback channels: Mono
  Mono: Playback [on]
...

$ amixer controls -c 1
numid=36,iface=CARD,name='Headphone Jack'
numid=37,iface=CARD,name='Headset Mic Jack'
numid=38,iface=MIXER,name='Headphone Switch'
numid=31,iface=MIXER,name='PCM Volume'
...
```



**Get the specified configuration of the sound card **

```
$ amixer cget -c 1 name='Speaker Switch'
numid=39,iface=MIXER,name='Speaker Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
```



**Set the specified configuration of the sound card **

```
$ amixer sget -c 1 numid=39,iface=MIXER,name='Speaker Switch' 1
```



**Speaker playback test **

```
$ amixer cset -c 1 name='Speaker Switch' 1
$ amixer cset -c 1 name='Output 2 Playback Volume' 20 20
$ amixer cset -c 1 name='Right Mixer Right Playback Switch' 1
$ amixer cset -c 1 name='Left Mixer Left Playback Switch' 1
$ aplay -D hw:1,0 1.wav
```

> amixer configuration for es8388 sound card



**Headphone playback test **

```
$ amixer cset -c 1 name='Headphone Switch' 1
$ amixer cset -c 1 name='Output 1 Playback Volume' 20 20
$ aplay -D hw:1,0 1.wav
```

> amixer configuration for es8388 sound card



## 24. Microphone

### Linux recording test

**mic recording test **

```
$ amixer cset -c 1 numid=25,iface=MIXER,name='Capture Digital Volume' 192 192 
$ amixer cset -c 1 numid=26,iface=MIXER,name='Capture Mute' 0
$ amixer cset -c 1 numid=27,iface=MIXER,name='Left Channel Capture Volume' 3
$ amixer cset -c 1 numid=28,iface=MIXER,name='Right Channel Capture Volume' 3
$ amixer cset -c 1 numid=42,iface=MIXER,name='Left PGA Mux' 2
$ amixer cset -c 1 numid=43,iface=MIXER,name='Right PGA Mux' 2
$ amixer cset -c 1 numid=44,iface=MIXER,name='Differential Mux' 1
$ arecord -D hw:1,0 a.wav -r 48000 -f S16_LE -d 5 -c 2
```

> amixer configuration for es8388 sound card



**Headset MIC recording test **

```
$ amixer cset -c 1 numid=25,iface=MIXER,name='Capture Digital Volume' 192 192 
$ amixer cset -c 1 numid=26,iface=MIXER,name='Capture Mute' 0
$ amixer cset -c 1 numid=27,iface=MIXER,name='Left Channel Capture Volume' 3
$ amixer cset -c 1 numid=28,iface=MIXER,name='Right Channel Capture Volume' 3
$ amixer cset -c 1 numid=42,iface=MIXER,name='Left PGA Mux' 2
$ amixer cset -c 1 numid=43,iface=MIXER,name='Right PGA Mux' 2
$ amixer cset -c 1 numid=44,iface=MIXER,name='Differential Mux' 0
$ arecord -D hw:1,0 a.wav -r 48000 -f S16_LE -d 5 -c 2
```

> amixer configuration for es8388 sound card



## 26. CAN

Applicable platform: RK3568/RK3576/RK3588

The RK part of the chip supports the native CAN interface, which is usually led out to the expansion pin. For the specific location, please check the extension pin part.

The chip's native CAN interface cannot be used directly, and an external level switching circuit needs to be added.



**Query the current network device **

```
$ ifconfig -a
```



**CAN configuration **

Disable CAN

```
$ ip link set can0 down
```

Set bit rate 500KHz

```
$ ip link set can0 type can bitrate 500000
```

Print can0 information

```
$ ip -details -statistics link show can0
```

Start CAN

```
$ ip link set can0 up
```



**CAN FD send **

Sent (standard frame, dataframe, ID: 123, date: DEADBEEF):

```
$ cansend can0 123#DEADBEEF
```

Send (standard frame, remote frame, ID: 123)

```
$ cansend can0 123#R
```



**CAN receive **

Start printing and wait for receiving.

```
$ candump can0
```



**Loop mode test **

After starting can, enter the io command to start the loop auto-test (the base address is configured according to the can started by the actual dts)

```
$ io -4 0xfea60000 0x8415
```

> 0xfea60000 DTS node for K8 CAN0

In loop mode, candump can be received after cansend.



**Example **

- end point 1

Turn on the loop mode test and listen can

```
root@linaro-alip:/# io -4 0xfea60000 0x8415
root@linaro-alip:/# candump can0
  can0  123   [4]  DE AD BE EF
```



- end point 2

Send command

```
root@linaro-alip:/# cansend can0 123#DEADBEEF
```



## 27. SPI

**Tool Location **

```
$ kernel/tools/spi/spidev_test.c
```



**Compile **

```
$ make CROSS_COMPILE=~/path-to-toolchain/gcc-xxxxx-toolchain/bin/xxxx-linux-gnu-
```

> Select the kernel CROSS_COMPILE



**Loop mode test **

```
$ spidev_test -D /dev/spidev0.0 -v -l -p "hello"
```

> Master as Master
>
> MISO, MOSI for short connection



- Example

```
root@linaro-alip:/data# ./spidev_test -D /dev/spidev0.0 -v -l -p "hello"
spi mode: 0x24
bits per word: 8
max speed: 500000 Hz (500 kHz)
TX | 68 65 6C 6C 6F __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __  |hello|
RX | 68 65 6C 6C 6F __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __  |hello|
```



## 28. PCI-E

Applicable platform: KICKPI-K8 RK3588 Android/Linux

At present, only RK3588 supports one 4Lane PCIE3.0 interface, and the theoretical transmission rate can reach 3.938GB/s.

Standard PCI-E expansion port can be connected to common PCIE USB expansion card, PCIE Ethernet expansion card, PCIE SSD expansion card on the market



PCIE to USB 3.0 Testing

* Hardware wiring (PCIE X1 module)

![image-20250103184113556](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250103184113556.png)

* software testing





### PCIE to SSD Testing

* Hardware wiring (PCIE X4 module)

![image-20250103184238105](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250103184238105.png)

* Software testing







## 29, Ethernet

Applicable platforms: present on all major platforms



### iperf test method

To be perfected



## 30、KEY test<a id="KEYtest"></a>

* Android tests using the command getevent:

```
console:/ # getevent
add device 1: /dev/input/event3
  name:     "sunxi-ir-uinput"
add device 2: /dev/input/event2
  name:     "gt9xxnew_ts"
add device 3: /dev/input/event0
  name:     "sunxi-ir"
add device 4: /dev/input/event1
  name:     "gpio-keys"
/dev/input/event2: 0001 014a 00000001
/dev/input/event2: 0003 0035 00000247
/dev/input/event2: 0003 0036 000001c3
/dev/input/event2: 0003 0030 0000001e
/dev/input/event2: 0003 0032 0000001e
```

* Linux test using evtest:

```
root@ubuntu2004:~# evtest 
No device specified, trying to scan all of /dev/input/event*
Available devices:
/dev/input/event0:      fe6e0030.pwm
/dev/input/event1:      rk805 pwrkey
/dev/input/event2:      rockchip-rk809 Headset
/dev/input/event3:      hdmi_cec_key
/dev/input/event4:      adc-keys
/dev/input/event5:      gpio-keys
Select the device event number [0-5]: 5
Input driver version is 1.0.1
Input device ID: bus 0x19 vendor 0x1 product 0x1 version 0x100
Input device name: "gpio-keys"
Supported events:
  Event type 0 (EV_SYN)
  Event type 1 (EV_KEY)
    Event code 2 (KEY_1)
Key repeat handling:
  Repeat type 20 (EV_REP)
    Repeat code 0 (REP_DELAY)
      Value    250
    Repeat code 1 (REP_PERIOD)
      Value     33
Properties:
Testing ... (interrupt to exit)
Event: time 1699275783.300685, type 1 (EV_KEY), code 2 (KEY_1), value 1
Event: time 1699275783.300685, -------------- SYN_REPORT ------------
Event: time 1699275783.573759, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.573759, -------------- SYN_REPORT ------------
Event: time 1699275783.610429, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.610429, -------------- SYN_REPORT ------------
Event: time 1699275783.647094, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.647094, -------------- SYN_REPORT ------------
Event: time 1699275783.647094, type 1 (EV_KEY), code 2 (KEY_1), value 0
Event: time 1699275783.647094, -------------- SYN_REPORT ------------
```













