# 04-Hardware Function Test





## 1. Extension Pins<a id="ExpansionPin"></a>

### K1 Pins List

![lQDPJwdL80_ad8TNIJ3NPi2w1GhOJ6gVEfcFI3nfzUimAA_15917_8349](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/lQDPJwdL80_ad8TNIJ3NPi2w1GhOJ6gVEfcFI3nfzUimAA_15917_8349.jpg)



### K1B Pins List

![image-20240914152349051](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240914152349051.png)

### K3 Pins List

![K3_产品详情页_10](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K3_产品详情页_10.jpg)





## 2. LED

The development board is equipped with 2 LEDs. The green LED is the power indicator light, which is always on by default when powered on.

The blue LED is the system working heartbeat light. If the heartbeat light keeps flashing, it means that the kernel is running normally.

In addition, the system working heartbeat light can refer to the following aspects to control the LED as other functions.



### User LED State Control

**Command Line LED State Control**

The default LED trigger mode is set to "heartbeat," under which the LED state cannot be manually controlled.

If you wish to achieve command-based LED state control, you can use the following command to set it to "none" first. Afterward, you can adjust the LED's brightness as needed. 

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

K1 development board's 40-pin extension port comes with 9 controllable GPIO (General Purpose Input/Output) pins by default.

Firstly, refer to the [Extension Pins](#ExpansionPin) section to confirm the positions and numbers of GPIO pins. Follow the steps below to control the status of a specific GPIO.



### Sys GPIO Control

When there is a need to configure the expansion pin as input, the default software gpio-led cannot meet the need

Release GPIO and control it through /sys/class/gpio

**PIN calculation**

Take GPIO1-D0 (gpio1-24) as an example:

```
Each GPIO group has 32 bits: 0-32
A (0-7) B (8-15) C (16-23) D (24-31)
GPIO1-D0 calculation Pin num = 32 * 1 + 24 = 56
```

**Step 1**

First, comment the corresponding GPIO pins. `/sys/class/gpio/export` can only import unregistered gpio

Disable the corresponding IO in the device tree

The device tree is located at:

```
kernel-5.10/arch/arm64/boot/dts/rockchip/
40pin:	rk3568-kickpi-extend-40pin.dtsi
20pin:	rk3568-kickpi-extend-20pin.dtsi
```

**Step 2**

Compile the image and re-burn

**Step 3**

Confirm that gpio is not registered

```
cat /sys/kernel/debug/pinctrl/pinctrl-rockchip-pinctrl/pinmux-pins
```

> Unregistered as follows: pin 56 (gpio1-24): (MUX UNCLAIMED) (GPIO UNCLAIMED)

**Step 4**

Register 56 and control it through /sys/class/gpio/export

```
// Register
root@kickpi:~# echo 56 > /sys/class/gpio/export
// Check if it is generated
root@kickpi:~# ls /sys/class/gpio/
export gpio56 gpiochip0 gpiochip352 unexport
// Node content after registration
root@kickpi:~# ls /sys/class/gpio/gpio56
active_low device direction edge power subsystem uevent value
root@kickpi:~#
```

Control gpio through the content under the node, commonly used as follows

```direction
direction
	in / out
	echo in > /sys/class/gpio/gpio56/direction
	echo out > /sys/class/gpio/gpio56/direction
value
	0 / 1
	cat /sys/class/gpio/gpio56/value 		// 读取
	echo 1 > /sys/class/gpio/gpio56/value	// 配置高电平
	echo 0 > /sys/class/gpio/gpio56/value  // 配置低电平
```



### User GPIO Level Control

>The default software expansion pins are configured as GPIO and are generally registered as this control method
>
>This method can control the pin to output high or low level



**View GPIO Registration List**

```
$ ls /sys/class/leds/

gpio1b0/ gpio1b2/ gpio1d4/ gpio3b6/ 
gpio1a4/ gpio1b1/ gpio1d0/ gpio3b5/ gpio4c4/
```

**Command Line GPIO State Control**

```
$ echo 1 > /sys/class/leds/gpio3b6/brightness
$ echo 0 > /sys/class/leds/gpio3b6/brightness
```



## 4. UART

###  Debug Serial Port Usage

**Check the DEBUG pin**

The debug serial port of the Kickpi development board is usually embedded in the expansion pins. You can view the corresponding DEBUG pin position in the [Expansion Pins Chapter](#ExpansionPin). They are usually arranged in the order of `UART_RX_DEBUG`, `UART_TX_DEBUG`, and `GND`.



**Debug serial line description**

The debug serial port cable currently provided has four wires, the red one is `VCC`, the green one is `TX`, the white one is `RX`, and the black one is `GND`. Generally, there is no need to connect `VCC`.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)



**Development board debugging serial port wiring**

* Debug serial port location wiring instructions

Connect debug line `TX` to `RX` on the mainboard, `RX` to `TX` on the mainboard, `GND` to `GND` on the mainboard, and do not need to connect `VCC`.

![image-20241231151848065](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231151848065.png)

>PIN on the motherboard: RX TX GND 
>
>PIN on the debug line: TX RX GND



**Download Serial Port Assistant Software**

You can download serial port assistant software on your own. The following steps use Mobaxterm as an example.

Mobaxterm Software Download Link: [Mobaxterm](https://mobaxterm.mobatek.net/)



**Mobaxterm Operation Steps**

1. Create a new session window.
2. Select the session window type as "serial."
3. Choose the serial port COM number (Open Windows Device Manager -> Check the COM number in the Ports section).
4. Select the serial port baud rate as 1500000.
5. Start the session window.



![image-20230519113450846](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230519113450846.png)





### Ordinary Serial Port Usage

1. Confirm the serial port number used

2. Check whether the corresponding node exists

```
$ ls /dev/ttyS*
```

3. Communication test using microcom

```
$ microcom -s 115200 /dev/ttyS4
```

>For example, the node found for serial port 4 is /dev/ttyS4 
>
>-s sets the baud rate to 115200



## 5. USB

### OTG Mode Switching

For the Android 13.0 system, USB defaults to OTG mode, capable of automatically switching between master and slave functions.

For Linux systems, USB defaults to HOST mode, and OTG is not currently supported.





### 6. PWM

RK3568 has 4 PWM modules, each with 4 channels, providing a total of 16 PWM channels.

### User-Level Configuration of PWM Output

The Extend 40Pin interface includes multiple PWM channels. Taking PWM3 channel as an example, follow the commands below to configure PWM:

After successfully configuring the parameters using the example below, you can measure the voltage of the PWM3 pin with a multimeter, and the correct voltage should be around 1.6V.

Example: Setting up PWM3 channel with a period of 10000ns, a duty cycle of 5000ns, and normal polarity.

```bash
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```





### 7. WIFI

The K1 development board is equipped with the RTL8822CS high-performance WIFI module.

Supports 2.4G/5G dual-frequency bands, WIFI 5, 2T2R.

#### *Graphical Interface WIFI Configuration

#### *Command Line WIFI Configuration





### 8. Bluetooth

The K1 development board is equipped with the RTL8822CS high-performance Bluetooth module.

#### *Graphical Interface Pairing Bluetooth Devices

#### *Command Line Pairing Bluetooth Devices





### 9. SATA Hard Disk

The K1 development board is equipped with a SATA3.0 hard disk interface.

#### *Hardware Wiring (Pic)

SATA hard disk wiring includes the power interface and data interface.

> **Note:** The SATA hard disk power interface is a PH2.0 connector, and a PH2.0 connector to SATA power cable needs to be purchased separately. The power part includes 5V and 12V, and must be connected to the hard disk after confirming the correct wire sequence; otherwise, it may burn the hard disk.

Keep the development board powered off, connect the hard disk, power on, and Android/Linux will automatically mount the hard disk partition.

> **Note:** It is recommended to partition the hard disk in advance and format the partition.

#### *Performance Testing





### 10. M.2 SSD

The K1 development board is equipped with a PCIe3.0 M.2 SSD hard disk interface.

For Android, only `NTFS` or `Fat32` format partitions are supported.

> It is recommended to format the solid-state drive in advance and create partitions in the supported format. If the SSD partition is not in a format supported by the system, you can follow the system prompts to format the partition, which will default to Fat32.

#### *Hardware Wiring (Pic)

The M.2 interface supports mainstream 2280 specifications hard disks and comes with fixing screws.

Make sure the development board is powered off, connect the hard disk, and power on.

#### Performance Testing

Test SSD model: Samsung PM981 256G

Test results:

| Platform        | Sequential Write Speed | Sequential Read Speed |
| --------------- | ---------------------- | --------------------- |
| K1 Android 13.0 | 243 MB/s               | 604 MB/s              |
| K1 Debian 11    |                        |                       |

> Unless there are special requirements, it is recommended to use the Fat32 format partition.





### 11. RTC

The K1 development board is equipped with the HYM8563 RTC chip, which can achieve power-off timekeeping.

#### *Hardware Wiring (Pic)

To test the RTC function, an external button battery must be connected, and the battery power must be sufficient.

#### Power-Off Timekeeping Test

Test Steps:

1. Connect the development board to the network and automatically synchronize the correct network time.
2. Disconnect the network and power supply to the development board.
3. After waiting for some time, reconnect the power supply to the development board.
4. If the system time is the same as the current time, the RTC function is normal.





### 12. LCD

The K1 development board is equipped with three LCD interfaces: one LVDS interface (can be reused as a MIPI interface), one MIPI interface, and one eDP interface.

The three LCD interfaces support three-screen display/touch separately.

> The default firmware has been adapted to the official screen, and one firmware can be compatible with multiple screens.
> It supports any one of the LCD interfaces.





#### MIPI Screen Hardware Wiring

**FPC Requirements**

Number of pins: 30, and the direction of the FPC is the same.

**Wiring Example**

![MIPI Wiring Example](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021170724701.png)

> Note: Before plugging or unplugging any FPC interface peripheral, ensure that the board is completely powered off.





#### LVDS Screen Hardware Wiring

**FPC Requirements**

Number of pins: 30, and the direction of the FPC is the same.

**Wiring Example**

![LVDS Wiring Example](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021171105002.png)

> Note: Before plugging or unplugging any FPC interface peripheral, ensure that the board is completely powered off.





#### eDP Screen Hardware Wiring

**FPC Requirements**

Number of pins: 30, and the direction of the FPC is the same.

**Wiring Example

**

![eDP Wiring Example](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231021171209133.png)

> Note: Before plugging or unplugging any FPC interface peripheral, ensure that the board is completely powered off.
> For eDP screens, an additional 12V power supply must be connected at the screen end.





### 13. MIPI Camera

#### *Hardware Wiring (Pic)

**FPC Requirements**

Number of pins: 40, and the direction of the FPC is the opposite.

> It is recommended to use the attached FPC, otherwise, it may cause damage to the screen.
> Before plugging or unplugging any FPC interface peripheral, ensure that the board is completely powered off.

#### Debian Preview Image

```bash
$ cheese
```

> After executing the command line to start the cheese program, the camera image will be displayed directly.





### 16. GPU

#### Debian GLmark2 Performance Test

The RK Linux file system comes with the glmark2-es performance test tool.

Connect the board to the display device, open a virtual terminal or a debugging serial port terminal, and execute the following command to start the GPU performance test:

```bash
$ source /rockchip-test/gpu/test_fullscreen_glmark2.sh

# The terminal prints the final test score.
=======================================================
                                  glmark2 Score: 471
=======================================================
```

> External displays can view GPU-rendered graphics, and debugging serial ports can view GPU-rendered logs.
> Depending on the GPU performance difference, it takes about 10 minutes to complete the performance test.





### 17. NPU

The RK3568 is equipped with a 1T computing power NPU unit.

#### Debian Image Classification Test

Open a virtual terminal or a debugging serial port terminal and execute the following command to start the NPU function test:

```bash
$ source /rockchip_test/npu2/npu_stress_test.sh

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





## 18. MPP

Rockchip MPP provides testing tools such as mpi_enc_test and mpi_dec_test.

You can directly use the command line to invoke MPI for hardware encoding and decoding tests.

### Hardware Encoding Tests

* H.264 encoding 4096x2160 100 frames test

```shell
$ mpi_enc_test -w 4096 -h 2160 -t 7 -o ./test.h264 -n 100
```

* H.265 encoding 4096x2160 100 frames test

```shell
$ mpi_enc_test -w 4096 -h 2160 -t 16777220 -o ./test.h265 -n 100
```

> Parameter interpretation:
>
>   - mpi_enc_test: MPP encoding test tool
>   - -w 4096 -h 2160: Specifies the video resolution as 4096x2160
>   - -t 16777220: Specifies the encoding type as H.265
>   - -o ./test.h265: Specifies the output file for encoded content
>   - -n 100: Specifies the number of encoded frames

### Hardware Decoding Tests

* Decode H.264 video test

```shell
$ mpi_dec_test -t 7 -i test.h264 -n 100
```

* Decode H.265 video test

```shell
$ mpi_dec_test -t 16777220 -i test.h265 -n 10
```

### Chromium Video Tests

Connect the board to a display device, open a virtual terminal, or debug serial terminal, and execute the following command to start Chromium video testing.

```shell
$ source /rockchip-test/chromium/test_chromium_with_video.sh
```





## 19. ADC

RK3568 is equipped with 8 channels of ADC, and the K1 development board exposes ADC3 channel to the expansion pin socket.

### Read ADC3 Value

```shell
$ cat /sys/bus/iio/devices/iio\:device0/in_voltage3_raw
```





## 20. WatchDog

RK3568 is equipped with one watchdog, and the watchdog is enabled by default on power-up. The kernel thread automatically feeds the watchdog.

### User-Level Configuration for Manual Feeding

You can use the following command to switch to user-level manual feeding. Make sure to execute the command every 44 seconds, or the system will restart.

```shell
$ echo A > /dev/watchdog
```
