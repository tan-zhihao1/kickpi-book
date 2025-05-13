# 03-Functional_Testing

This document is intended for users to quickly use or test the peripheral interfaces of the motherboard.

SDK - hereinafter refers to the source code path.
console - hereinafter refers to the motherboard debugging command-line console.
ADB - Android Debug Bridge command-line tool, hereinafter refers to the environment where ADB can be run.

## Wi-Fi

The RK356x series development boards are equipped with the RTL8822CS high-performance Wi-Fi module.
It supports dual-band 2.4G/5G, Wi-Fi 5, and 2T2R.

![image-20231215153316513](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153316513.png)

### Configuring Wi-Fi via the Graphical Interface

![image-20240120091252595](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240120091252595.png)

### Connecting to Wi-Fi via Commands

```shell
$ sudo nmcli dev wifi connect "your_WiFi_name" password "WiFi_password" ifname wlan0
```

> your_WiFi_name: The name of the Wi-Fi you want to connect to.
> WiFi_password: The password of the Wi-Fi you want to connect to.

* After connecting, the Wi-Fi information can be found in this directory:

```shell
$ ls /etc/NetworkManager/system-connections
wifi_oranth_5G.nmconnection
```

* To cancel the automatic connection to a specific Wi-Fi, for example, Wi-Fi: wifi_oranth_5G

```shell
$ sudo rm -rf /etc/NetworkManager/system-connections/wifi_oranth_5G.nmconnection
```

## Bluetooth

The RK356x series development boards are equipped with the RTL8822CS high-performance Bluetooth module. The following describes the Bluetooth testing methods.

### Android

In the Android system, testing is generally performed through the graphical interface.
Open the system settings.

![image-20250427114520715](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427114520715.png)

View the connected devices and select to pair with a new device.

![image-20250427115144556](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427115144556.png)

After the scanning results are available, the scanned devices will appear.

![image-20250427115405365](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427115405365.png)

Click on the device you want to connect to, and the following prompt will appear to start the pairing process.

![image-20250427115549717](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427115549717.png)

After both devices select to connect, the connection will be successful.

![image-20250427133429295](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427133429295.png)

### Linux

* Testing via the graphical interface

1. Click on the Bluetooth icon.
2. Open the Bluetooth Devices.

![image-20250427190127291](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427190127291.png)

Click on Scan for devices.

![image-20250427190308274](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427190308274.png)

First, click on Trust the device, then right-click to open the menu, and click on Pair. A pairing pop-up window will appear in the upper right corner. Click on Allow pairing.

![image-20250427191115089](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427191115089.png)

After successful pairing

![7a87d42001319614706698f2b1de9f7](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/7a87d42001319614706698f2b1de9f7.jpg)

After successful connection, a connection icon will appear on the right side of the device. After a normal shutdown, the device will automatically connect when you turn on the board next time. If you are connecting a Bluetooth headset, you can play music.

* Testing via the command line

The command line generally uses the `bluetoothctl` tool. The following example shows how to use `bluetoothctl` to connect a Bluetooth headset.

Enter the `bluetoothctl` command line.

```
bluetoothctl
```

> After entering the `bluetoothctl` command line, it will be similar to: [bluetooth]#
> Controller represents the Bluetooth device on the ARM board, and Device represents the connected Bluetooth headset.

Follow the steps below to initialize the Bluetooth device and connect the Bluetooth headset.

View the help information.

```
help
```

```
power on
agent on
default-agent
```

Start scanning.

```
scan on
```

Suppose the address of the Bluetooth headset is: 00:11:22:33:44:55

```
trust 00:11:22:33:44:55
```

Pair.

```
pair 00:11:22:33:44:55
```

Connect.

```
connect 00:11:22:33:44:55
```

View the information of the Bluetooth headset.

```
info 00:11:22:33:44:55
```

Disconnect.

```
disconnect 00:11:22:33:44:55
```

If you don't want the Bluetooth headset to connect automatically, you can delete the pairing information.

```
remove 00:11:22:33:44:55
```

For more information on command-line Bluetooth connection, please refer to https://blog.csdn.net/chenjk10/article/details/90317028

## SATA Hard Drive

The K1 development board is equipped with one SATA 3.0 hard drive interface.

### Hardware Wiring

The SATA hard drive wiring consists of a hard drive power supply interface and a hard drive data interface.

> **Special Note**: The SATA hard drive power supply interface is a PH2.0 terminal. You need to purchase a PH2.0 terminal to SATA power cable separately.
> The power supply part includes 5V and 12V. You must confirm the correct wire sequence before connecting the hard drive; otherwise, the hard drive may be damaged.

Keep the development board powered off, connect the hard drive, and then power on the board. Android/Linux will automatically mount the hard drive partitions.

> **Special Note**: It is recommended to partition the hard drive in advance and choose an appropriate partition format.

![image-20231215153119268](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153119268.png)

## M.2 SSD

The K1 development board is equipped with one PCIe 3.0 M.2 SSD hard drive interface.
The Android system only supports `NTFS` or `Fat32` format partitions.

> It is recommended to format the solid-state drive in advance and create partitions in a supported format.
> If the SSD partition is not in a format supported by the system, you can also follow the system prompts to format the partition. The default format is Fat32.

### Hardware Wiring

The M.2 interface supports the current mainstream 2280 specification hard drives and is equipped with fixing studs.
Ensure that the development board is powered off, connect the hard drive, and then power on the board.

![image-20231215153247719](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153247719.png)

### Performance Testing

Test SSD model: Samsung PM981 256G

Test results:

| Platform         | Sequential Write Speed | Sequential Read Speed |
| ---------------- | ---------------------- | --------------------- |
| K1 Android 13.0  | 243 MB/s               | 604 MB/s              |
| K1 Debian 11     |                        |                       |

> If there are no special requirements, it is recommended to use the Fat32 format for partitioning.

## RTC<a id='RTC'> </a>

The K1 development board is equipped with the HYM8563 RTC chip, which can save the time even when the power is off.

### Hardware Wiring

To test the RTC function, you must connect an external coin cell battery and ensure that the battery has sufficient power.

![image-20231215153345278](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153345278.png)

### Power-Off Time Saving Test

Test steps:

1. Connect the development board to the network and automatically synchronize the correct network time.
2. Disconnect the network connection and the power supply of the development board.
3. Wait for some time and then reconnect the power supply of the development board.
4. If the system time is consistent with the current time, the RTC function is normal.

## MIPI Camera

The K1 development board supports a single-channel MIPI CSI 4Lane camera interface and can connect up to two cameras at most.
The K3 development board supports a dual-channel MIPI CSI 4Lane camera interface and can connect up to four cameras at most.

### Hardware Wiring

![image-20231215153036711](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153036711.png)

**Cable Requirements**

The number of pins is 40, and the cable direction is reverse.

> It is recommended to use the cable included with the screen; otherwise, the screen may be damaged.
> Before plugging or unplugging any FPC interface peripheral, ensure that the board is completely powered off.

### Preview with Cheese

Enter the following command in the command line:

```shell
$ cheese
```

> After executing the command to start the `cheese` program, the camera image will be directly displayed.
> There is currently an ISP library issue in Ubuntu, and the preview effect will be very poor and dark.

![image-20230906140719725](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230906140719725.png)

### Preview Images with `gst-launch-1.0` in Debian

The Debian file system has a built-in `gst-launch-1.0` test script.

```
/rockchip-test/camera/camera_rkisp_test.sh
```

> Depending on the number of connected cameras, the `/dev/video` node number may shift, causing the script to fail to open the correct camera.
> In this case, you can modify the script to specify the correct video number, for example, `device=/dev/video-camera0`.

## HDMI in

The K8 supports one HDMI input. Currently, the EDID in the driver code supports the following resolutions:

```
3840x2160P60, 3840x2160P50, 3840x2160P30, 3840x2160P25, 3840x2160P24,
1920x1080P60, 1920x1080P50, 1920x1080P30, 1920x1080i60, 1920x1080i50,
1600x900P60, 1440x900P60, 1280x800P60,
1280x720P60, 1280x720P50, 1024x768P60,
720x576P50, 720x480P60, 720x576i50, 720x480i60,
800x600P60, 640x480P60
```

The supported input formats include:

```
RGB888, YUV420, YUV422, YUV444
```

## GPU

**GPU Utilization**

```
$ cat /sys/devices/platform/*gpu/utilisation
```

> You can move the mouse, windows, or perform GPU tests and check the GPU utilization to determine if hardware acceleration is working.

### GLmark2 Performance Test in Debian

The RK Linux file system has a built-in `glmark2-es` performance test tool.
Connect the board to a display device, open a virtual terminal or a debugging serial port terminal, and execute the following command to start the GPU performance test.

```
$ source /rockchip-test/gpu/test_fullscreen_glmark2.sh

# The final test score will be printed in the terminal
=======================================================
                                  glmark2 Score: 471
=======================================================
```

> You can view the GPU-rendered graphics on an external display (score is approximately 49), and view the GPU rendering logs on the debugging serial port (score is approximately 203).
> Depending on the GPU performance, it may take about 10 minutes to complete the performance test.

**K8 Test Results**

```
$ source /rockchip-test/gpu/test_fullscreen_glmark2.sh
=======================================================
                                  glmark2 Score: 676 
=======================================================
```

> When connected to HDMI_OUT0, the test result is 676.

## NPU

The RK3568 is equipped with an NPU unit with a computing power of 0.8T.

**Viewing NPU Usage**

```
$ watch cat /sys/kernel/debug/rknpu/load
$ cat /sys/kernel/debug/rknpu/load
```

### Image Classification in Debian

Open a virtual terminal or a debugging serial port terminal, and execute the following command to start the NPU function test.

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

> The content after `Begin perf...` shows the performance data of multiple runs of the model:
> - Each record shows the run number, the corresponding elapsed time (`Elapse Time`), and the frames per second (`FPS`). For example, `0: Elapse Time = 2.85ms, FPS = 351.12` means that during the first run of the model inference, it took a total of 2.85 milliseconds, and based on this, it can process 351.12 frames of data per second (the FPS is calculated by dividing 1000 by the elapsed time for each run, and the unit is converted to frames per second).
>
> The `---- Top5 ----` section presents the top 5 categories with the highest probabilities in the model inference output results, along with their corresponding probability values and category numbers:
> - `0.935059 - 156` means that the model believes the input data (such as an image) is most likely to belong to category number 156, with a corresponding probability of 0.935059. This relatively high probability indicates that the model has a relatively high confidence in this judgment.
> - The subsequent `0.057037 - 155`, `0.003881 - 205`, `0.003119 - 284`, `0.000172 - 285` list the category numbers and probability values of the second to fifth highest probability categories respectively. These probability values decrease sequentially, indicating that the model's confidence in these categories also gradually decreases.

**K8 NPU Stress Test Results**

When connected to HDMI_OUT0

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

> From the printed information, it can be seen that the average number of frames processed per second is 450.

### Object Detection in Debian

The detection data stream can be obtained from a camera or a video file.
The detection results are directly output to the display, so you need to connect a display to view the results.

```
$ source /rockchip-test/npu2/rknn_yolov5_demo_Linux.sh
```

## MPP

Rockchip MPP provides testing tools such as `mpi_enc_test` and `mpi_dec_test`.
You can directly call MPI through the command line for hardware encoding and decoding tests.

### Hardware Encoding Test

* H.264 encoding test for 100 frames at 4096x2160

```
$ mpi_enc_test -w 4096 -h 2160 -t 7 -o ./test.h264 -n 100
```

* H.265 encoding test for 100 frames at 4096x2160

```
$ mpi_enc_test -w 4096 -h 2160 -t 16777220 -o ./test.h265 -n 100
```

> Parameter Explanation:
> - `mpi_enc_test`: MPP encoding test tool
> - `-w 4096 -h 2160`: Specify the encoding video resolution as 4096x2160
> - `-t 16777220`: Specify the encoding type as H.265
> - `-o ./test.h265`: Specify the output file for the encoded content
> - `-n 100`: Specify the number of frames to be encoded

### Hardware Decoding Test

* H.264 video decoding test for 100 frames

```
$ mpi_dec_test -t 7 -i test.h264 -n 100
```

* H.265 video decoding test for 100 frames

```
$ mpi_dec_test -t 16777220 -i test.h265 -n 100
```

**K8 Encoding and Decoding Test Results**

```
H.264 encoding test for 100 frames at 4096x2160, fps: 44.65
H.265 encoding test for 100 frames at 4096x2160, fps: 45.53
H.264 decoding test for 100 frames at 4096x2160, fps: 206.22
H.265 decoding test for 100 frames at 4096x2160, fps: 386.86
```

### Chromium Video Test

Connect the board to a display device, open a virtual terminal or a debugging serial port terminal, and execute the following command to start the Chromium video test.

```
$ source /rockchip-test/chromium/test_chromium_with_video.sh
```

## ADC

The RK3568 is equipped with 8 ADC channels. The K1 development board leads out the ADC3 channel to the expansion pin header.

### Reading the ADC3 Value

```
$ cat /sys/bus/iio/devices/iio\:device0/in_voltage3_raw
```

## Watchdog

The RK3568 is equipped with one watchdog. The watchdog is enabled by default after power-on, and the kernel thread automatically feeds the dog.

### User-Level Configuration for Manual Dog Feeding

You can use the following command to switch to user-level manual dog feeding. You must repeat the following command every 44 seconds to feed the dog; otherwise, the system will restart.

```
$ echo A > /dev/watchdog
```

## FAN

The fan's power supply voltage is 5V. The connection method is shown in the following figure.

![image-20231215152208539](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215152208539.png)

## 4G Communication Module

Currently, it is compatible with and supports the Quecte EC20 and Quecte EC200M-CN modules.

### USB_4G Module with MiniPCIe Interface

![image-20231215153217640](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153217640.png)

Testing Method:
As long as the USB_4G module, antenna, and SIM card are properly installed, the system will automatically detect and obtain an IP address when it boots up.

### uART_4G Module with UART Interface

![1733198014256](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1733198014256.png)

For the UART module, you need to manually execute the connection script.

```shell
$ sudo /etc/ppp/peers/quectel-pppd.sh /dev/ttyS4
```

> `/dev/ttyS4` (UART4): The UART port to be used.

![image-20241203115419752](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241203115419752.png)

## Speaker

![image-20231215153423975](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231215153423975.png)

### Linux Playback Test

**ALSA Tools**

In the Linux system, you can generally control the sound card configuration and play audio through ALSA tools. The relevant tools are already built into the current Linux system software.
If the relevant tools are not installed, you can execute the following command to install them.

```
$ sudo apt install alsa-utils
```

> After installation, commands such as `aplay`, `arecord`, and `amixer` can be used.

**Tool Usage Instructions**

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

> You can use the `-h` option with the commands to view the usage instructions for the corresponding commands.

**Listing and Viewing Sound Cards**

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

> The speaker's sound card is es8388, which is sound card 1.

**Viewing the Sound Card Control Configuration List**

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

**Getting the Specified Configuration of the Sound Card**

```
$ amixer cget -c 1 name='Speaker Switch'
numid=39,iface=MIXER,name='Speaker Switch'
  ; type=BOOLEAN,access=rw------,values=1
  : values=on
```

**Setting the Specified Configuration of the Sound Card**

```
$ amixer sget -c 1 numid=39,iface=MIXER,name='Speaker Switch' 1
```

**Speaker Playback Test**

```
$ amixer cset -c 1 name='Speaker Switch' 1
$ amixer cset -c 1 name='Output 2 Playback Volume' 20 20
$ amixer cset -c 1 name='Right Mixer Right Playback Switch' 1
$ amixer cset -c 1 name='Left Mixer Left Playback Switch' 1
$ aplay -D hw:1,0 1.wav
```

> The `amixer` configuration is applicable to the es8388 sound card.

**Headphone Playback Test**

```
$ amixer cset -c 1 name='Headphone Switch' 1
$ amixer cset -c 1 name='Output 1 Playback Volume' 20 20
$ aplay -D hw:1,0 1.wav
```

> The `amixer` configuration is applicable to the es8388 sound card.

## Microphone

### Linux Recording Test

**MIC Recording Test**

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

> The `amixer` configuration is applicable to the es8388 sound card.

**Headset MIC Recording Test**

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

> The `amixer` configuration is applicable to the es8388 sound card.

## PCI-E

Applicable Platforms: KICKPI-K8 RK3588 Android/Linux

Currently, only the RK3588 supports one 4Lane PCIe 3.0 interface, with a theoretical transmission rate of up to 3.938GB/s.
The standard PCI-E expansion port can be externally connected to commonly available PCIE USB expansion cards, PCIE Ethernet expansion cards, and PCIE SSD expansion cards on the market.

### PCIE to USB 3.0 Test

* Hardware Wiring (PCIE X1 Module)

![image-20250103184113556](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250103184113556.png)

* Software Testing

### PCIE to SSD Test

* Hardware Wiring (PCIE X4 Module)

![image-20250103184238105](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250103184238105.png)

* Software Testing

## Ethernet

Applicable Platforms: All platforms

### Android System

Connect a working Ethernet cable, and the Android system interface will display a wired network icon.

![image-20250512151221225](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512151221225.png)

You can access web pages normally in the browser.

![image-20250512151504759](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512151504759.png)

### Linux System

## KEY Test<a id="KEYtest"> </a>

### Android System

Click the "loader" button, which is configured as the "Volume Up" button in the Android system, and a volume progress bar will appear on the right side of the display screen.
Click the "power" button to put the system into sleep mode or wake it up.
Click the "Reset" button to power off and then power on the board, and it will restart.

## UART to RS485/RS232 Module

### Wiring Method

RS485 Module (Can be powered by 3.3V/5V):

![image-20250324143115205](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324143115205.png)

> The wiring method for connecting the 4-pin end to the board's UART port is as follows (corresponding top to bottom):
> - Mainboard: RX     TX    GND  VCC
> - Module:  RXD  TXD  GND   VCC
>
> The wiring method for connecting the 3-pin end to the RS485 device is as follows (corresponding top to bottom):
> - Module:       A     GND    B  
> - RS485 Device: A     GND    B 

RS232 Module (Can be powered by 3.3V/5V):

![image-20250324142614631](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324142614631.png)

> The wiring method for connecting the 4-pin end to the board's UART port is as follows (corresponding top to bottom):
> - Mainboard: TX     RX    GND  VCC
> - Module:  RXD  TXD  GND   VCC
>
> The wiring method for connecting the 3-pin end to the RS232 device is as follows (corresponding top to bottom):
> - Module:       RXD    GND      TXD
> - RS232 Device: TXD    GND      RXD 

### Testing Method:

**Android** 

You can use a serial port app or the `microcom` command in the command line for verification:

The `microcom` command is as follows:

```shell
# microcom -s 115200 /dev/ttyAS3
```

> The received content will be printed, and the sent content will not be printed.

**Linux System** 

You can install and use `minicom` for verification:

Install `minicom`

```shell
$ sudo apt update
$ sudo apt install minicom
```

The received content will be printed, and the sent content will not be printed.

```shell
# minicom -b 115200 -D /dev/ttyAS3
```

> Press `Ctrl+A` to enter the control mode. Use the `B - Z` commands, and `Z` is for help.

> | Ctrl+A X | Exit the program                        |
> | -------- | --------------------------------------- |
> | Ctrl+A W | Enable/disable auto line feed (default: disabled) |
> | Ctrl+A E | Enable/disable input display (default: disabled) |
> | Ctrl+A C | Clear the screen                        |

If the serial port is abnormally occupied after exiting:

```shell
$ minicom -s 
```

> Serial port setup >> F   Hardware Flow Control: Turn it off >> Save setup as dfl: Save >> Exit from Minicom: Exit

Or you can use `echo` to directly send data:

```shell
# stty -F /dev/ttyAS3 -a 	// You can view the default baud rate
# stty -F /dev/ttyAS3 115200 	// You can set the baud rate
# echo "123" > /dev/ttyAS3
```