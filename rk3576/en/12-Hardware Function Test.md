# 12-Instructions for using hardware functions

## Expand pin<a id="ExpansionPin-K7"></a>

**K7 Extended Pin List**

![K7_40pin](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/K7_40pin.jpg)

## LED

The development board is equipped with 2 LEDs, the green LED is the power indicator, and it is always on by default when powered on.

The blue LED is the system working heartbeat light. If the heartbeat light continues to flash, it indicates that the core is running normally.

In addition, the system working heartbeat light can refer to the following aspects to control LED as other functions.

### User layer control LED status

** Command line control LED status **

The default LED trigger mode is heartbeat, and the LED state cannot be controlled by humans in this mode.

If you want to control the LED state by command, you can set it to none by the following command, and then set the brightness of the LED

```bash
$ echo none > /sys/class/leds/work/trigger
$ echo 1 > /sys/class/leds/work/brightness
$ echo 0 > /sys/class/leds/work/brightness
```

Restore heartbeat mode

```bash
$ echo heartbeat > /sys/class/leds/work/trigger
```



## GPIO

First, by expanding the pin chapter, confirm the pin position and number of the GPIO, and refer to the following steps to control the output high or low level of the specified GPIO

** List the registered GPIO control nodes **

```
$ ls /sys/class/leds/
fan       GPIO3_B0  GPIO3_D5  mmc2::      SDMMC0_PWREN
GPIO0_A5  GPIO3_D4  GPIO4_D1  PCIE_PWREN  work
```

**Control GPIO output level status**

GPIO3_D4 输出高电平
```
$ echo 1 >  /sys/class/leds/GPIO3_D4/brightness
```
GPIO3_D4 输出低电平
```
$ echo 0 >  /sys/class/leds/GPIO3_D4/brightness
```



## UART

### Debug serial port use

** Baud rate: 1500000 **

> The USB to serial cable connected to the seat also needs to support 1500000 baud rate to communicate normally



** View DEBUG pin **

Kickpi development board debugging serial port is generally embedded in the expansion pin, you can see the corresponding DEBUG pin position through [Expand pin](#ExpansionPin-K7), generally arranged in order as "UART_RX_DEBUG", "UART_TX_DEBUG", "GND".



** Debugging serial cable instructions **

The debugging serial cable currently provided has four wires, the red one is'VCC ', the green one is'TX', the white one is'RX ', and the black one is'GND'. Generally, it is not necessary to connect to'VCC '.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

> The same is true for other debugging Creative Tools, just connect TX RX GND



** Development board debugging serial port wiring **

* Debugging serial port location wiring instructions

The debugging line "TX" is connected to "RX" on the main board, "RX" is connected to "TX" on the main board, and "GND" is connected to "GND" on the main board. There is no need to connect "VCC".

> PIN on main board: RX TX GND
>
> PIN of debugging line: TX RX GND



** Download Serial Port Assistant Software **

Serial assistant software can be downloaded by yourself. The following is an example of Mobaxterm

Mobaxterm software address download: https://mobaxterm.mobatek.net/



** Mobaxterm operation steps **

1. Create a new session window
2. Select the session window type as serial
3. Select the COM number of the serial port (open the Windows System Facility Management - > Port interface to view the COM number)
4. Select the serial port baud rate 1500000
5. Start the session window



![image-20230519113450846](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230519113450846.png)



### Ordinary serial port use

Test UART8

```
 $ microcom -s 115200 /dev/ttyS8
```



## USB

USB 3.0 - Type A (1 - 3 ports)

### ** USB mouse/keyboard test **

Support USB mouse recognition, normal use
Support USB keyboard recognition, normal use

### USB Type C

Support HOST mode, U disk, mouse and other devices access
Support DEVICE mode, adb and other functions
Support DP v1.4 display output mode
Support automatic recognition switching mode

## HDMI TX

HDMI v2.1
Supports up to 4K@120Hz
Output data format: RGB/YUV444/YUV422/YUV420 8/10-bit
Supports CEC (Consumer Electronics Control) and ARC (Audio Return Channel)
HDCP v2.3 and HDCP v1.4

## ADC

You can check the corresponding ADC location and channel number through  [Expansion Pin](#ExpansionPin-K7)

### ** Read ADC value **

```
$ cat /sys/bus/iio/devices/iio\:device0/in_voltage*_raw
```

> Example:
>
> Read the voltage value of channel 4 
>
> $ cat /sys/bus/iio/devices/iio\:device0/in_voltage4_raw



## SD card

Support SD memory card, onboard Micro SD Card interface

### Hardware wiring

![image-20250110152735195](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110152735195.png)



## IR

Support infrared remote control, currently adapted to KICKPI remote control



## Ethernet

Supports two Gigabit Ethernet channels



## WIFI & BTS

Onboard 8822CS module



## 4G/5G module

4G has been adapted to EC20/EC200 modules
5G has been adapted to RG200U module

### ** Hardware wiring **

![image-20250110152808407](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110152808407.png)



## Headphone

Support 3.5mm headphone jack audio output

### ** Hardware wiring **

![image-20250110152818915](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110152818915.png)



## MIC

Support the recording seat, connect to the MIC microphone head for recording.

```
PCM in:
 Format Name:   S16_LE, S24_LE
        Rate:   min=8000Hz      max=96000Hz
    Channels:   min=1           max=2
 Sample bits:   min=16          max=32
 Period size:   min=8           max=131072
Period count:   min=2           max=32768
```

### ** Hardware wiring **

![image-20250110152824237](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110152824237.png)



## Speaker Output

Support left channel and right channel speaker audio output

```
PCM out:
 Format Name:   S16_LE, S24_LE
        Rate:   min=8000Hz      max=96000Hz
    Channels:   min=1           max=2
 Sample bits:   min=16          max=32
 Period size:   min=8           max=131072
Period count:   min=2           max=32768
```

### ** Hardware wiring **

![image-20250110152830177](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110152830177.png)



## RTC

Onboard RTC hym8563
Support access to RTC battery, power down RTC use battery to keep working

### ** Hardware wiring **

![image-20250110153056417](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110153056417.png)



## FAN

### ** Hardware wiring **

![image-20250110153117263](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110153117263.png)

### ** Switch control **

Turn on the fan

```
$ echo 255 > /sys/class/leds/fan/brightness
```

Turn off the fan

```
$ echo 0 > /sys/class/leds/fan/brightness
```



## M.2 SSD

K7 development board, equipped with one PCIE 2.0 M.2 SSD hard disk interface

### ** Hardware wiring **

![image-20250110153138855](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250110153138855.png)



## CAN

Supporting the CAN FD standard, CAN FD inherits most of the characteristics of CAN, such as the same physical layer, two-wire serial communication protocol, based on non-destructive arbitration technology, distributed real-time control, reliable error handling and detection mechanism, etc. At the same time, CAN FD makes up for CAN's shortcomings in bus bandwidth and data length.

** Query current CAN device **

```
$ ifconfig -a
...
can0      Link encap:UNSPEC    Driver rk3576_canfd
          NOARP  MTU:16  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0 
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0 
          collisions:0 txqueuelen:10 
          RX bytes:0 TX bytes:0 
          Interrupt:63 
...
```

** CAN start **

Disable CAN

```
$ ip link set can0 down
```

Set 1M baud rate for arbitration segment and 3M baud rate for data segment

```
$ ip link set can0 type can bitrate 1000000 dbitrate 3000000 fd on
```

Print can0 information

```
$ ip -details link show can0
```

Start CAN

```
$ ip link set can0 up
```

** CAN send **

Sent (standard frame, dataframe, ID: 123, date: DEADBEEF)

```
$ cansend can0 123#DEADBEEF
```

Sent (extended frame, dataframe, ID: 00000123, date: DEADBEEF)

```
$ cansend can0 00000123##1DEADBEEF
```

** CAN receive **

Start printing and wait for receiving.

```
$ candump can0
```

** Loop mode test **

```
$ ip link set can0 down
$ ip link set can0 type can bitrate 500000 sample-point 0.8 dbitrate 2000000 sample-point 0.8 fd on loopback on
$ ip -details -statistics link show can0
$ ip link set can0 up
$ echo 4096 > /sys/class/net/can0/tx_queue_len
$ candump can0 &
```

In loop mode, the candump can be received after cansend, indicating that the controller is working normally.



## PWM

Extend 40Pin interface, including multi-channel PWM, see [Extended Pin](#ExpansionPin-K7)

pwmX_Ych_Z, X represents the controller id, Y represents the total number of channels currently supported by the controller, and Z represents the channel id.

> pwm0_2ch_1: pwm@27331000
>
> pwm2_8ch_6: pwm@2ade6000
>
> pwm2_8ch_7: pwm@2ade7000

List the relevant PWM nodes

```
$ ls /sys/class/pwm/
pwmchip0  pwmchip1  pwmchip2  pwmchip3
```

You can view the corresponding pwm dts node

```
$ cat /sys/class/pwm/pwmchip0/device/uevent | grep FULLNAME
OF_FULLNAME=/pwm@27331000
```

>Represents pwnchip0 corresponding to bit PWM0 channel 1
>

Configure PWM channel

Example: Set PWM0_CH1 channel, period 10000ns, duty cycle 5000ns, polarity is normal

```
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```

> After the configuration parameters are successfully set according to the example, the PWM0 pin can be measured with a multimeter, and the correct voltage should be about 1.6V



## SPI

** Loop mode test **

List spi device nodes

```
$ ls /dev/spi*
/dev/spidev4.0
```

Specify equipment for testing

```
$ spidev_test -D /dev/spidev4.0 -v -l -p "hello"
```

> MISO, MOSI for hardware shorting

example

```
root@linaro-alip:/data# ./spidev_test -D /dev/spidev4.0 -v -l -p "hello"
spi mode: 0x24
bits per word: 8
max speed: 500000 Hz (500 kHz)
TX | 68 65 6C 6C 6F __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __  |hello|
RX | 68 65 6C 6C 6F __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __  |hello|
```



## GPU

```
ARM Mali G52 MC3
OpenGL ES 1.1, 2.0 and 3.2, OpenCL 2.1, Vulkan 1.2
```

**View GPU Usage Rate**

```
cat /sys/devices/platform/*gpu/utilisation
```

> 示例：实时查看使用率
>
> $ watch -n 1 'cat /sys/devices/platform/*gpu/utilisation'
>
> 移动鼠标或窗口或进行GPU测试，可以查看GPU使⽤率来确定硬加速是否有⽤起来

**GLmark2 性能测试**

Rockchip 提供 npu 测试脚本

```
$ ls /rockchip-test/gpu
gpu_test.sh  test_fullscreen_glmark2.sh  test_normal_glmark2.sh test_offscreen_glmark2.sh  test_stress_glmark2.sh
```

Debian / Ubuntu 文件系统已内置 glmark2-es 性能测试工具

1. 板卡连接显示设备

2. 使用虚拟终端或调试串口终端，执行以下命令开始 GPU 性能测试

```
$ source /rockchip-test/gpu/test_fullscreen_glmark2.sh
=======================================================
                                  glmark2 Score: 337 
=======================================================
```

> glmark2测试分数为 337



## NPU

```
6 TOPS*@INT8
Support int4/int8/int16/FP16/BF16/TF32
Support deep learning frameworks: TensorFlow, Caffe, Tflite, Pytorch, Onnx NN, Android NN, etc
```

**Debian / Ubuntu**

Rockchip 提供 npu 测试脚本

```
# ls /rockchip-test/npu2
model  npu_freq_scaling.sh  npu_stress_test.sh  npu_test.sh
```

npu 频率测试脚本 `npu_freq_scaling.sh`

```
usage()
{
    echo "Usage: npu_freq_scaling.sh [test_second] [every_freq_stay_second]"
    echo "example: ./npu_freq_scaling.sh 3600 30"
    echo "means npu_freq_scaling.sh will run 1 hour and every cpu frequency stay 30s"
}
```

示例：npu变频运行60秒，每10秒变频一次

```
# ./npu_freq_scaling.sh 60 10
test will run 60 seconds
every npu frqeucny will stay 10 seconds
set ddr frequency to 700000000
set ddr frequency to 300000000
set ddr frequency to 700000000
set ddr frequency to 950000000
set ddr frequency to 500000000
set ddr frequency to 700000000
======TEST SUCCESSFUL, QUIT=====
```

npu 压力测试脚本 `npu_stress_test.sh`

```
# ./npu_stress_test.sh
rknn_api/rknnrt version: 2.0.0b0 (35a6907d79@2024-03-24T10:31:14), driver version: 0.9.7
model input num: 1, output num: 1
input tensors:
  index=0, name=input, n_dims=4, dims=[1, 224, 224, 3], n_elems=150528, size=150528, fmt=NHWC, type=INT8, qnt_type=AFFINE, zp=0, scale=0.007812
output tensors:
  index=0, name=MobilenetV1/Predictions/Reshape_1, n_dims=2, dims=[1, 1001, 0, 0], n_elems=1001, size=2002, fmt=UNDEFINED, type=FP16, qnt_type=AFFINE, zp=0, scale=1.000000
custom string: 
Begin perf ...
   0: Elapse Time = 2.85ms, FPS = 351.12
   1: Elapse Time = 2.70ms, FPS = 370.37
   2: Elapse Time = 2.59ms, FPS = 386.85
   3: Elapse Time = 2.69ms, FPS = 371.61
   4: Elapse Time = 2.62ms, FPS = 381.97
   5: Elapse Time = 2.61ms, FPS = 383.44
   6: Elapse Time = 2.58ms, FPS = 387.75
   7: Elapse Time = 2.70ms, FPS = 370.51
   8: Elapse Time = 2.64ms, FPS = 378.36
   9: Elapse Time = 2.68ms, FPS = 372.44
---- Top5 ----
0.935059 - 156
0.057037 - 155
0.003881 - 205
0.003119 - 284
0.000172 - 285
```

>在 `Begin perf...` 后的内容展示了模型多次运行的性能数据：
>
>- 每次记录了运行的序号以及对应的耗时（`Elapse Time`）和每秒处理帧数（`FPS`）。例如，`0: Elapse Time = 2.85ms, FPS = 351.12`表示第 1 次运行模型推理时，总共花费了 2.85 毫秒的时间，由此计算出每秒可以处理 351.12 帧的数据（FPS 的计算方式就是 1000 除以每次的耗时，单位换算后得到每秒的帧数）。
>
>`---- Top5 ----` 部分呈现了模型推理输出结果中概率排名前 5 的类别及其对应的概率值和类别编号：
>
>- `0.935059 - 156`表示模型认为输入的数据（比如可能是一张图像）最有可能属于编号为 156 的类别，其对应的概率高达 0.935059，这个概率值相对较高，说明模型对该判断有比较高的置信度。
>- 后续的`0.057037 - 155`、`0.003881 - 205`、`0.003119 - 284`、`0.000172 - 285`分别列出了概率排名第 2 到第 5 的类别编号及其概率值，这些概率值依次递减，表明模型对它们属于相应类别判断的把握程度也逐渐降低。



## MPP

```
H.265 HEVC Main10 L5.1 yuv444: 4K 120fps
H.264 AVC High10 L5.1 yuv422: 4K 60fps
H.264 MVC up to 1080P 60fps
VP9 Profile0/2 L5.1: 4K 120fps
AVS2 Profile0/2 L10.2.6: 4K 120fps
AV1 Main10 L5.3: 4K 120fps
4K@60fps video encoders for H.264/H.265
```

**调试信息**

开启调试信息

```
$ export mpp_syslog_perror=1
$ echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

>开启调试信息后，在调用硬件编解码会有类似如下日志信息
> [  893.134037] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1333 us hw 1312 us
> [  893.167444] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1381 us hw 1313 us
> [  893.200503] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1420 us hw 1313 us

关闭调试信息

```
$ export mpp_syslog_perror=0
$ echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

**Debian / Ubuntu**

Rockchip 提供了 MPP 相关工具进行使用

**硬编码测试**

mpi_enc_test

```
usage: mpi_enc_test [options]
 -i       input_file          input frame file                       
 -o       output_file         output encoded bitstream file          
 -w       width               the width of input picture             
 -h       height              the height of input picture            
 -hstride hor_stride          the horizontal stride of input picture 
 -vstride ver_stride          the vertical stride of input picture   
 -f       format              the format of input picture            
 -t       type                output stream coding type              
 -tsrc    source type         input file source coding type          
 -n       max frame number    max encoding frame number              
 -g       gop reference mode  gop_mode:gop_len:vi_len                
 -rc      rate control mode   set rc_mode, 0:vbr 1:cbr 2:fixqp 3:avbr
 -bps     bps target:min:max  set tareget:min:max bps                
 -fps     in/output fps       set input and output frame rate        
 -qc      quality control     set qp_init:min:max:min_i:max_i        
 -fqc     frm quality control set fqp min_i:max_i:min_p:max_p        
 -s       instance_nb         number of instances                    
 -v       trace option        q - quiet f - show fps                 
 -l       loop count          loop encoding times for each frame     
 -ini     ini file            encoder extra ini config file          
 -slt     slt file            slt verify data file                   
 -sm      scene mode          scene_mode, 0:default 1:ipc 
```

* 编码H.264 4096x2160 100帧测试

```
$ mpi_enc_test -w 4096 -h 2160 -t 7 -o ./test.h264 -n 100
```

> 查看结果
>
> $ tail -f /var/log/syslog
>
> kickpi mpp[3557]: mpi_enc_test: chn 0 encode 100 frames time 3763 ms delay  27 ms fps 26.57 bps 10605252

* 编码H.265 4096x2160 100帧测试

```
$ mpi_enc_test -w 4096 -h 2160 -t 16777220 -o ./test.h265 -n 100
```

> 查看结果
>
> $ tail -f /var/log/syslog
>
> kickpi mpp[3560]: mpi_enc_test: chn 0 encode 100 frames time 4086 ms delay  36 ms fps 24.47 bps 19594276

**硬解码测试**

mpi_dec_test

```
 usage: mpi_dec_test [options]
 -i       input_file   input bitstream file                                 
 -o       output_file  output decoded frame file                            
 -w       width        the width of input bitstream                         
 -h       height       the height of input bitstream                        
 -t       type         input stream coding type                             
 -f       format       output frame format type                             
 -n       frame_number max output frame number                              
 -s       instance_nb  number of instances                                  
 -v       trace option q - quiet f - show fps                               
 -slt     slt file     slt verify data file                                 
 -help    help         show help                                            
 -bufmode buffer mode  hi - half internal (default) i -internal e - external
```

* 解码H.264 4096x2160 100帧测试

```
$ mpi_dec_test -t 7 -i test.h264 -n 100
```

> 查看结果
>
> $ tail -f /var/log/syslog
>
> kickpi mpp[3564]: mpi_dec_test: decode 100 frames time 596 ms delay  25 ms fps 167.53

* 解码H.265 4096x2160 100帧测试

```
$ mpi_dec_test -t 16777220 -i test.h265 -n 100
```

> 查看结果
>
> $ tail -f /var/log/syslog
>
> kickpi mpp[3569]: mpi_dec_test: decode 100 frames time 803 ms delay  49 ms fps 124.47

**chromium视频测试**

板卡连接显示设备，打开虚拟终端 或 调试串口终端，执行以下命令开始 chromium 视频测试

```
$ source /rockchip-test/chromium/test_chromium_with_video.sh
```

ubuntu 中 rockchip chromium 和 gstreamer 配置硬解码存在兼容性问题！

默认配置 chromium 进行调用，若chromium未调用硬解码，需要以下命令进行修复

```
$ source /rockchip-test/chromium/chromium_mpp_fix.sh
```

> 配置后，默认 chromium 可调用硬解码

**gstreamer视频测试**

```
sudo GST_DEBUG=2 gst-launch-1.0 playbin uri=file:///usr/local/test.mp4 video-sink="autovideosink" audio-sink=fakesink
```

> 如果有mpp调⽤的字样，说明硬件解码成功调用。

ubuntu 中 rockchip chromium 和 gstreamer 配置硬解码存在兼容性问题！

默认配置 chromium 进行调用，若需要 gstreamer 调用硬解码，需要以下命令进行修复

```
$ source /rockchip-test/gstreamer/gstreamer_mpp_fix.sh
```

> 此命令需要联网，保证'apt update'成功
>
> 配置后，默认 gstreamer 可调用硬解码

