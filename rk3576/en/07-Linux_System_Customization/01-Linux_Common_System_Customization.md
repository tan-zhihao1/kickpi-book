# 01-Linux_Common_System_Customization

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the command-line console of the motherboard.
* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

## ADB Function

[ADB Tutorial](../../../common/en/adb/ADB Tutorial.md)

## SCP File Transfer

```
$ scp $local_path username@$ip:$target_path
```

$local_path - Local file path
$username - Username
$ip - Motherboard IP
$target_path - Target path

Example:

```
$ scp .\1.wav linaro@192.168.77.165:/home/linaro/Desktop/
```

## Screen Display Orientation Configuration

> Debian 12

**Get usage help**

```
xrandr -help
```

**View screen information**

```
(console)/# xrandr
Screen 0: minimum 320 x 200, current 1920 x 1080, maximum 16384 x 16384
HDMI-1 connected 1920x1080+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
   1920x1080     60.00*+  60.00    50.00    50.00    59.94
   1920x1080i    60.00    50.00    50.00    59.94
   1680x1050     59.88
   1280x1024     75.02    60.02
   1440x900      59.90
   1280x960      60.00
   1360x768      60.02
   1152x864      75.00
   1280x720      60.00    50.00    50.00    59.94
   1024x768      75.03    70.07    60.00
   832x624       74.55
   800x600       72.19    75.00    60.32    56.25
   720x576       50.00    50.00    50.00
   720x480       60.00    60.00    59.94    59.94
   640x480       75.00    72.81    66.67    60.00    59.94    59.94
   720x400       70.08
DSI-1 connected (normal left inverted right x axis y axis)
   800x1280      60.51 +
DP-1 disconnected (normal left inverted right x axis y axis)
```

**Rotation control**

> According to xrandr, the current display device is HDMI-1.

Set the rotation of HDMI-1.

Normal display of the picture

```
(console)$ xrandr --output HDMI-1 --rotate normal	
```

Rotate right, clockwise rotation of 90 degrees

```
(console)$ xrandr --output HDMI-1 --rotate right	
```

Rotate left, clockwise rotation of 270 degrees

```
(console)$ xrandr --output HDMI-1 --rotate left		
```

Invert, clockwise rotation of 180 degrees

```
(console)$ xrandr --output HDMI-1 --rotate inverted			
```

**Touch calibration**

Step 1: Install tools
```
$ apt update
$ apt install xinput
$ apt install xinput-calibrator
```

Step 2: View the Device and ID. It can be known that the ID of the goodix-ts device is 11.
```
$ xinput_calibrator --list 
Device "goodix-ts" id=11
```

Step 3: Reset the transpose matrix and calibration matrix. This is very crucial. The transpose matrix needs to be adjusted according to the screen rotation, and the calibration matrix can be set to the default value.
```
$ xinput set-prop $id --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1

$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
// xinput set-prop 11 --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
```

Modify the touch direction (can be modified according to the name). Default direction
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
```

Modify the touch direction (can be modified according to the name). 90 degrees to the left
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
```

Modify the touch direction (can be modified according to the name). 90 degrees to the right
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
```

Modify the touch direction (can be modified according to the name). Rotate 180 degrees
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
```

Step 4: Start calibration
```
$ xinput_calibrator -v --device $id
// xinput_calibrator -v --device 11
```

## Language Configuration

Debian 12

To modify the system language, open the command-line terminal and execute the following commands. Restart the system for the changes to take effect.

* Set the language to English

```bash
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" > /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" > /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
$ reboot
```

* Set the language to Chinese

```bash
$ locale-gen zh_CN.UTF-8
$ sed -i 's/^# *\(zh_CN.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=zh_CN.UTF-8" > /etc/default/locale
$ echo "export LC_ALL=zh_CN.UTF-8" > /etc/profile.d/zh_CN.sh
$ echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
$ reboot
```

## Time Zone Configuration

> The changes take effect after the system is restarted.

* Set the time zone to Asia/Shanghai

```
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     
```

* Set the time zone to America/New York

```
$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```

## Network Configuration

### Debian 12

Use the `ifconfig` and `route` commands (temporary settings)

This method will be invalid after the system is restarted, but it can quickly test the configuration of a fixed IP.

1. **Use the `ifconfig` command to set the IP address, subnet mask, and broadcast address**:

```
sudo ifconfig eth0 <IP> netmask <netmask_num> broadcast <broadcast ip>
```

For example:

```
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0 broadcast 192.168.1.255
```

> - `sudo`: Run the command with administrator privileges.
> - `ifconfig`: Used to configure network interfaces.
> - `eth0`: Network interface name. You can view your own network interface name through `ifconfig -a`, which may be `eth0`, `ens33`, or other names.
> - `<IP address>`: The fixed IP address you want to set.
> - `<Subnet mask>`: Usually `255.255.255.0` or other subnet masks.
> - `<Broadcast address>`: Generally calculated based on the IP address and subnet mask. For example, for the IP address `192.168.1.100` and the subnet mask `255.255.255.0`, the broadcast address is `192.168.1.255`.

2. **Use the `route` command to set the gateway**:

```
sudo route add default gw <gw addr> eth0
```

For example:

```
sudo route add default gw 192.168.1.1 eth0
```

> - `route`: Used to configure routes.
> - `add default gw <Gateway address>`: Add the default gateway.
> - `<Gateway address>`: The gateway address of your network.

Use the `/etc/network/interfaces` file (long-term settings)

This method can achieve a long-term and stable fixed IP configuration.

1. **Edit the `/etc/network/interfaces` file**:

```
sudo vi /etc/network/interfaces
```

> - `sudo`: Run the command with administrator privileges.

2. **Add or modify the network interface configuration**:

```
auto eth0
iface eth0 inet static
   address 192.168.1.100
   netmask 255.255.255.0
   gateway 192.168.1.1
   # If you need to set the DNS server, you can add the following lines
   dns-nameservers 8.8.8.8 8.8.4.4
```

> - `auto eth0`: Indicates that the `eth0` interface is automatically enabled when the system starts.
> - `iface eth0 inet static`: Indicates that the `eth0` interface uses a static IP configuration.
> - `address`: Set the IP address.
> - `netmask`: Set the subnet mask.
> - `gateway`: Set the gateway.
> - `dns-nameservers`: Set the DNS server. Here, Google's DNS servers are used. You can use other DNS servers, such as `114.114.114.114`, etc.

3. **Save and exit the editor**:

- In `vi`, press `ESC`, then enter `:wq`, and press the `ENTER` key to save and exit.

4. **Restart the network service or the system**:

```
sudo service networking restart
```

Or directly restart the system:

```
sudo reboot
```

## NFS Configuration

**Environment configuration**

```
sudo apt install nfs-kernel-server
sudo apt install rpcbind
sudo apt install nfs-common
```

**Server side**

1. Configure the shared files

```
$ mkdir /home/kickpi/nfs_share
$ chmod 777 /home/kickpi/nfs_share
$ vi /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
# 
/home/kickpi/nfs_share *(rw,sync,no_subtree_check,insecure)
```

2. Start the service

```
sudo service nfs-kernel-server restart
```

3. View the currently shared files on the server to prove the sharing result

```
$  showmount -e localhost
Export list for localhost:
/home/kickpi/nfs_share *
```

**Client side**

1. View the shared files on the server

```
showmount -e 192.168.19.173
Export list for 192.168.19.173:
/home/kickpi/nfs_share *
```

2. Mount the folder

```
$ mkdir nfs_tmp
$ sudo mount -t nfs 192.168.19.173:/home/kickpi/nfs_share nfs_tmp/
```

3. Mount successfully

```
$ ls nfs_tmp/
1.txt
```

## Chrome Hardware Acceleration

Test platform: K7 Debian 12 Chrome

#### Test method

1. Copy 1080P and 4K videos to the board and decode them locally through the Chrome browser.
2. Check the GPU usage:

```
cat /sys/devices/platform/*gpu/utilisation   // The display result is in percentage.
```

3. Open and print the frame decoding log:

```
export mpi_debug=1
export mpp_debug=1
export h264d_debug=1
export mpp_syslog_perror=1
echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

## Backup the File System

When the file system is modified, it needs to be copied to other boards of the same type.

You can export the file system in the following way and then reflash it.

Connect a USB flash drive (or other storage device) to the board. The size should be at least 16GB or more because the generated image will be relatively large.

Run our packaging script on the board.

```
ff_export_rootfs (Storage device path) -t ext4
```

The format of the generated package name

```
*_ext4_*.img
```

> Note:
>
> Since the file system is large, it takes a long time to back up the system.
>
> Since the maximum single-file size of FAT32 is 4GB, it is not recommended to use a storage device with the FAT32 file system for backup.

## 4G/5G Configuration

### Script configuration

Configure the 4G/5G mobile module to start up, detect the presence of the module, and configure the dial-up connection.

```
(console)$ cat /usr/bin/hardware-optimization 
4g_config() {
    wait_time=30
        for((i=1;i<=$wait_time;i++));
        do 
                if [ -c /dev/ttyUSB2 ]; then
                        echo "$i: 4g /dev/ttyUSB2 exists and is a character device." >> $LOG_FILE
                        /usr/bin/4G_dialing.sh
            break;
                else
                        echo "$i: 4g /dev/ttyUSB2 does not exist or is not a character device." >> $LOG_FILE
                        sleep 1
                fi
        done
}
```

> By default, the system will only attempt to dial up once at startup. In case of poor signal or communication abnormalities, the dial-up may fail.
>
> If there is no network, you can run the dial-up script to retry.

The dial-up script is compatible with RG200U / EC200 / EC20.

```
(console)$ ls /usr/bin/4G_dialing.sh
```

The RG200U module dials up automatically. You can force the configuration and restart the module.

```
(console)$ 4G_dialing.sh force
```

> This operation will reset the module. You need to wait for `/dev/ttyUSB2` to be remounted, and then the `4G_dialing.sh` script will perform the dial-up configuration again.
>
> That is:
>
> 1. $ 4G_dialing.sh force
>
> 2. Wait for `/dev/ttyUSB2`
>
> 3. $ 4G_dialing.sh

### Internet access identification

**Check if the module is connected**

Check the log to determine.

```
$ cat /tmp/kickpi-hardware.log 
1: 4g /dev/ttyUSB2 does not exist or is not a character device.
2: 4g /dev/ttyUSB2 does not exist or is not a character device.
...
24: 4g /dev/ttyUSB2 does not exist or is not a character device.
25: 4g /dev/ttyUSB2 exists and is a character device.
```

> `/dev/ttyUSB2 exists and is a character device.`
>
> Indicates that the device is successfully recognized.

Or check if `/dev/ttyUSB2` exists.

```
$ ls /dev/ttyUSB*
/dev/ttyUSB0  /dev/ttyUSB1  /dev/ttyUSB2  /dev/ttyUSB3  /dev/ttyUSB4
```

**Check if the dial-up is successful**

```
(console)$ ifconfig
enxca7f24fb0e94: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.138.192.102  netmask 255.255.255.0  broadcast 10.138.192.255
        inet6 fe80::583f:ed51:782d:318d  prefixlen 64  scopeid 0x20<link>
        ether ca:7f:24:fb:0e:94  txqueuelen 1000  (Ethernet)
        RX packets 181  bytes 15185 (14.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 194  bytes 17394 (16.9 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

> The actual device name starts with `enx*`. Please use the actual name.

**Ping the network for testing**

```
(console)$ ping www.baidu.com -I enxca7f24fb0e94
PING www.wshifen.com (103.235.46.115) from 10.138.192.102 enxca7f24fb0e94: 56(84) bytes of data.
64 bytes from 103.235.46.115 (103.235.46.115): icmp_seq=1 ttl=45 time=1094 ms
64 bytes from 103.235.46.115 (103.235.46.115): icmp_seq=2 ttl=45 time=3924 ms
64 bytes from 103.235.46.115 (103.235.46.115): icmp_seq=3 ttl=45 time=1870 ms
```

> Specify the device `enx3e003a5bd6ad` to ping the network for testing.

## Other

**The display service will be restarted when Debian updates the system for the first time**

To be compatible with different chips, the general Debian system will install various differential packages, such as libmali and isp packages, according to the chip when `/etc/init.d/rockchip.sh` is started for the first time. After the installation, the display service will be restarted. If it is an independent project, you can handle these differences when creating the image.

## GPU

```
ARM Mali G52 MC3
OpenGL ES 1.1, 2.0 and 3.2, OpenCL 2.1, Vulkan 1.2
```

**Check the GPU usage**

```
cat /sys/devices/platform/*gpu/utilisation
```

> Example: Check the usage in real-time.
>
> $ watch -n 1 'cat /sys/devices/platform/*gpu/utilisation'
>
> Move the mouse, window, or perform a GPU test to check the GPU usage and determine if hardware acceleration is working.

**GLmark2 performance test**

Rockchip provides NPU test scripts.

```
$ ls /rockchip-test/gpu
gpu_test.sh  test_fullscreen_glmark2.sh  test_normal_glmark2.sh test_offscreen_glmark2.sh  test_stress_glmark2.sh
```

The Debian / Ubuntu file system has the glmark2-es performance test tool pre-installed.

Use the virtual terminal or debug serial terminal to execute the following command to start the GPU performance test.

```
root@linaro-alip:/# source /rockchip-test/gpu/test_offscreen_glmark2.sh 
run glmark2 x11 with offscreen......
arm_release_ver: g13p0-01eac0, rk_so_ver: 10
=======================================================
    glmark2 2023.01
=======================================================
    OpenGL Information
    GL_VENDOR:      ARM
    GL_RENDERER:    Mali-G52
    GL_VERSION:     OpenGL ES 3.2 v1.g13p0-01eac0.0fd2effaec483a5f4c440d2ffa25eb7a
    Surface Config: buf=32 r=8 g=8 b=8 a=8 depth=24 stencil=0 samples=0
    Surface Size:   800x600 windowed
=======================================================
=======================================================
                                  glmark2 Score: 1405 
=======================================================
```

> The glmark2 test score for 800x600 is 1405.
>
> The test results are for reference only. The actual score should be measured in practice.

## NPU

```
6 TOPS*@INT8
Support int4/int8/int16/FP16/BF16/TF32
Support deep learning frameworks: TensorFlow, Caffe, Tflite, Pytorch, Onnx NN, Android NN, etc
```

**Debian / Ubuntu**

Rockchip provides NPU test scripts.

```
# ls /rockchip-test/npu2
model  npu_freq_scaling.sh  npu_stress_test.sh  npu_test.sh
```

The NPU frequency test script `npu_freq_scaling.sh`

```
usage()
{
    echo "Usage: npu_freq_scaling.sh [test_second] [every_freq_stay_second]"
    echo "example: ./npu_freq_scaling.sh 3600 30"
    echo "means npu_freq_scaling.sh will run 1 hour and every cpu frequency stay 30s"
}
```

Example: Run the NPU frequency scaling test for 60 seconds, and change the frequency every 10 seconds.

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

The NPU stress test script `npu_stress_test.sh`

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

> The content after `Begin perf...` shows the performance data of multiple runs of the model:
>
> - Each record shows the run number, the corresponding elapsed time (`Elapse Time`), and the frames per second (`FPS`). For example, `0: Elapse Time = 2.85ms, FPS = 351.12` means that during the first run of the model inference, it took a total of 2.85 milliseconds, and thus it can process 351.12 frames per second (the FPS is calculated by dividing 1000 by the elapsed time for each run to get the frames per second after unit conversion).
>
> The `---- Top5 ----` section presents the top 5 categories with the highest probabilities in the model inference output results, along with their corresponding probability values and category numbers:
>
> - `0.935059 - 156` means that the model believes the input data (such as an image) is most likely to belong to category number 156, with a corresponding probability as high as 0.935059. This relatively high probability value indicates that the model has a relatively high confidence in this judgment.
> - The subsequent `0.057037 - 155`, `0.003881 - 205`, `0.003119 - 284`, and `0.000172 - 285` list the category numbers and probability values of the second to fifth highest probability categories respectively. These probability values decrease in sequence, indicating that the model's confidence in their belonging to the corresponding categories also gradually decreases.

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

**Debug information**

Enable debug information

```
$ export mpp_syslog_perror=1
$ echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

> After enabling the debug information, there will be similar log information when calling hardware encoding and decoding.
> [  893.134037] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1333 us hw 1312 us
> [  893.167444] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1381 us hw 1313 us
> [  893.200503] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1420 us hw 1313 us

Disable debug information

```
$ export mpp_syslog_perror=0
$ echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

**Debian / Ubuntu**

Rockchip provides MPP-related tools for use.

**Hardware encoding test**

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
```



