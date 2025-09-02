# 01-Linux常用系统定制



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## ADB 功能

[ADB 教程](..\..\..\common\zh\adb\ADB教程.md)



## SCP 文件传输

**用法**

```
usage: scp [-346ABCOpqRrsTv] [-c cipher] [-D sftp_server_path] [-F ssh_config]
           [-i identity_file] [-J destination] [-l limit]
           [-o ssh_option] [-P port] [-S program] source ... target
```

**快速使用**

```
$ scp $local_path usrname@$ip:$target_path
```

> $local_path - 本地文件路径
>
> $username - 用户名
>
> $ip - 主板ip
>
> $target_path - 目标路径

示例：

```
$ scp .\1.wav linaro@192.168.77.165:/home/linaro/Desktop/
```



## 屏幕显示方向配置

> Debian12

 **获取使用帮助**

```
xrandr -help
```

**查看屏幕信息**

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

**旋转控制**

>根据xrandr，已知目前已连接显示设备 HDMI-1，DSI-1

对 HDMI-1 进行旋转设置

画面正常显示

```
(console)$ xrandr --output HDMI-1 --rotate normal	
```

向右旋转，顺时针旋转90度

```
(console)$ xrandr --output HDMI-1 --rotate right	
```

向左旋转，顺时针旋转270度

```
(console)$ xrandr --output HDMI-1 --rotate left		
```

反转，顺时针旋转180度

```
(console)$ xrandr --output HDMI-1 --rotate inverted			
```

**触摸校准**

第一歩 安装工具
```
$ apt update
$ apt install xinput
$ apt install xinput-calibrator
```

第二步 查看 Device 和 ID，可知 goodix-ts 设备id 为 11
```
$ xinput_calibrator --list 
Device "goodix-ts" id=11
```

第三歩 重置转置矩阵和校准矩阵，这里非常关键，转置矩阵需要根据屏幕旋转，校准矩阵设置默认值即可
```
$ xinput set-prop $id --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1

$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
// xinput set-prop 11 --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
```

修改触摸方向（可根据名字修改） 默认方向
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
```

修改触摸方向（可根据名字修改） 向左90度
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
```

修改触摸方向（可根据名字修改） 向右90度
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
```

修改触摸方向（可根据名字修改） 旋转180度
```
$ xinput set-prop $id 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
```

第四步 开始校准
```
$ xinput_calibrator -v --device $id
// xinput_calibrator -v --device 11
```



## 语言配置

debian12

修改系统语言，请打开命令行终端，执行以下命令。重新启动使修改生效

* 设置英文语言

```bash
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" > /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" > /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
$ reboot
```

* 设置中文语言

```bash
$ locale-gen zh_CN.UTF-8
$ sed -i 's/^# *\(zh_CN.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=zh_CN.UTF-8" > /etc/default/locale
$ echo "export LC_ALL=zh_CN.UTF-8" > /etc/profile.d/zh_CN.sh
$ echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
$ reboot
```



## 时区配置

> 命令执行后，重启生效

* 设置时区亚洲/上海

```
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     
```

* 设置时区美国/纽约

```
$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```



## 网络配置

使用 `ifconfig` 和 `route` 命令（临时设置）

这种方法在系统重启后会失效，但可以快速测试固定 IP 的配置。

1. **使用 `ifconfig` 命令设置 IP 地址、子网掩码和广播地址**：

```
sudo ifconfig eth0 <IP> netmask <netmask_num> broadcast <broadcast ip>
```

例如：

```
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0 broadcast 192.168.1.255
```

> - `sudo`：以管理员权限运行命令。
> - `ifconfig`：用于配置网络接口。
> - `eth0`：网络接口名称，你可以通过 `ifconfig -a` 查看自己的网络接口名称，可能是 `eth0`、`ens33` 或其他名称。
> - `<IP 地址>`：你想要设置的固定 IP 地址。
> - `<子网掩码>`：通常是 `255.255.255.0` 或其他子网掩码。
> - `<广播地址>`：一般是根据 IP 地址和子网掩码计算得出，例如，对于 `192.168.1.100` 和 `255.255.255.0` 的子网掩码，广播地址是 `192.168.1.255`。

2. **使用 `route` 命令设置网关**：

```
sudo route add default gw <gw addr> eth0
```

例如：

```
sudo route add default gw 192.168.1.1 eth0
```

> - `route`：用于配置路由。
> - `add default gw <网关地址>`：添加默认网关。
> - `<网关地址>`：你的网络网关地址。



使用 `/etc/network/interfaces` 文件（长期设置）

这种方法可以实现长期稳定的固定 IP 配置。

1. **编辑 `/etc/network/interfaces` 文件**：

```
sudo vi /etc/network/interfaces
```

> - `sudo`：以管理员权限运行命令。

2. **添加或修改网络接口配置**：

```
auto eth0
iface eth0 inet static
   address 192.168.1.100
   netmask 255.255.255.0
   gateway 192.168.1.1
   # 如果需要设置 DNS 服务器，可以添加以下行
   dns-nameservers 8.8.8.8 8.8.4.4
```

   > - `auto eth0`：表示在系统启动时自动启用 `eth0` 接口。
   > - `iface eth0 inet static`：表示 `eth0` 接口使用静态 IP 配置。
   > - `address`：设置 IP 地址。
   > - `netmask`：设置子网掩码。
   > - `gateway`：设置网关。
   > - `dns-nameservers`：设置 DNS 服务器，这里使用了 Google 的 DNS 服务器，你可以使用其他 DNS 服务器，如 `114.114.114.114` 等。

3. **保存并退出编辑器**：

- 在 `vi` 中，按 `ESC`，然后输入`:wq` ，`ENTER`键保存并退出。

4. **重启网络服务或系统**：

```
sudo service networking restart
```

或者直接重启系统：

```
sudo reboot
```



## 图像化界面配置WIFI热点

> Ubuntu、Debian

1.确保连接以太网
2.单击网络图标打开图像化网络配置界面（Debian系统为右键 “两台PC图标”）

![image-20250901152628037](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250901152628037.png)

3.添加WiFi节点

![image-20250901152731237](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250901152731237.png)

4.选择Wi-Fi类型

![image-20250901152814149](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250901152814149.png)

5.进行WiFi网络节点配置

![image-20250901153026817](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250901153026817.png)

> SSID:WiFi名称
>
> Mode：模式
>
> Device：使用的设备

6.配置WiFi热点的安全属性

![image-20250901154557971](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250901154557971.png)

> None: 无密码
>
> WPA/WPA2/WPA3 Personal : 常用加密模式
>
> WPA3 Personal： 只支持WPA3
>
> Enhanced Open：公共网络的安全标准



## 命令行配置WIFI热点（AP）模式

### 使用物理接口创建热点模式

安装依赖和create_ap

```shell
$ sudo apt update
$ sudo apt-get install git util-linux hostapd dnsmasq iptables iproute2 haveged  make
$ git clone https://github.com/oblique/create_ap
$ cd */create_ap
$ sudo make install
```

创建热点MyAccessPoint，密码：12345678，共享eth0网络

```shell
$ sudo create_ap wlan0 eth0 MyAccessPoint 12345678 &
```

> 如果出现 dnsmasq: failed to bind DHCP server socket: Address already in use
> 因为create_ap 需要启动 dnsmasq，但是dnsmasq已经启动了
> lsof -i 找到dnsmasq对应的PID，kill -9 < PID> 掉就行

使用create_ap后 想恢复WiFi 节点

```shell
$ sudo create_ap --fix-unmanaged
```



## NFS配置

**环境配置**

```
sudo apt install nfs-kernel-server
sudo apt install rpcbind
sudo apt install nfs-common
```

**服务器端**

1. 配置共享的文件

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

2. 启动服务

```
sudo service nfs-kernel-server restart
```

3. 查看当前服务器共享文件，证明共享成果

```
$  showmount -e localhost
Export list for localhost:
/home/kickpi/nfs_share *
```

**客户端**

1. 查看服务器共享文件

```
showmount -e 192.168.19.173
Export list for 192.168.19.173:
/home/kickpi/nfs_share *
```

2. 挂载文件夹

```
$ mkdir nfs_tmp
$ sudo mount -t nfs 192.168.19.173:/home/kickpi/nfs_share nfs_tmp/
```

3. 挂载成功

```
$ ls nfs_tmp/
1.txt
```

  

## Chrome 硬件加速

测试平台：K7 Debian12 Chrome

**测试方法**

​	1.拷贝1080P及4K的视频到板子，通过chrome浏览器本地解码。

​	2.查看GPU占用：

```
cat /sys/devices/platform/*gpu/utilisation   //显示结果单位为%
```

​	3.打开帧解码日志并打印：

```
export mpi_debug=1
export mpp_debug=1
export h264d_debug=1
export mpp_syslog_perror=1
echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```



## eMMC分区

**注意：**重新分区均需要重新烧录，如果系统不想被格式化，请先[备份rootfs](#backup_rootfs)

**修改有两种方法：**

* 完整镜像修改

参考[固件解包和打包](../08-进阶/固件解包和打包.md)，修改parameter.txt文件后重新打包

* SDK内修改

修改对应板子型号路径下文件，后重新编译

RK3576：

```
SDK$ device/rockchip/rk3576//parameter.txt
```

**修改的内容：**

重新分区主要修改分区文件parameter.txt

例如:将剩余所有空间放到根目录下

```
mtdparts=:0x00002000@0x00004000(uboot),0x00002000@0x00006000(misc),0x00020000@0x00008000(boot),0x00040000@0x00028000(recovery),0x00010000@0x00068000(backup),-@0x00078000(rootfs:grow)
```

> 文件分区规则：分区大小:分区起始地址(分区名称)
>
> 分区大小：0x01c00000*512Byte/1024/1024/1014 ≈ 14G
>
> 分区起始地址=前一个分区起始地址+前一个分区大小
>
> 最后一个分区格式为：-@0x0xxxxx(xxx:grow)  -表示剩余所有空间自适应

Linux系统需要额外加一步修改

```
$ sudo mount -o loop rootfs.img rootfs
$ sudo vim rootfs/etc/fstab 
```

将oem userdate的自动挂载注释掉



## 备份文件系统

> 注意：这种方式替换后的rootfs 可能存在mount挂载UUID问题 可以blkid查看正确的UUID 然后修改/etc/fstab

接好U盘在板子上，大小至少16GB以上，打包出来的镜像会比较大

脚本位于网盘：

```
rk3576_data\3-SoftwareData\Linux_backup_rootfs_script
```

将脚本拷贝到板子Linux系统上运行

```shell
$ sudo chmod +x ./ff_export_rootfs
$ sudo ./ff_export_rootfs /mnt/usb -t ext4
```

> ff_export_rootfs (存储设备路径) -t ext4
>
> 生成的包名格式如：(系统) _ (存储类型) _ (时间戳).img
>
> /mnt/usb 为U盘挂载的目录
>
> 由于文件系统较大，备份系统需要等待较长时间
>
> 由于 FAT32 单个文件不超4G，不建议使用该格式文件系统存储设备进行备份

重新打包，将rootfs.img替换进完整镜像，参考[固件解包和打包](../08-进阶/固件解包和打包.md)

也可以对需要替换这个系统的板子单独烧录rootfs.img



## 4G/5G配置

### 脚本配置

4G/5G移动模块开机配置，检测模块是否存在，进行配置拨号

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

> 开机默认仅会拨号一次，在信号不好或通讯异常情况，可能会存在拨号失败的情况
>
> 若无网络，可运行拨号脚本进行重试



拨号脚本，已适配 RG200U / EC200 / EC20

```
(console)$ ls /usr/bin/4G_dialing.sh
```



RG200U由模块自动拨号，可通过强制配置并重启此模块

```
(console)$ 4G_dialing.sh force
```

> 此操作会重置模块，需要等待 /dev/ttyUSB2 重新挂载后，4G_dialing.sh 重新进行拨号配置
>
> 即：
>
> 1. $ 4G_dialing.sh force
>
> 2. 等待 /dev/ttyUSB2
>
> 3. $ 4G_dialing.sh



### 上网识别

**判断是否接入模块**

查看日志进行判断

```
$ cat /tmp/kickpi-hardware.log 
1: 4g /dev/ttyUSB2 does not exist or is not a character device.
2: 4g /dev/ttyUSB2 does not exist or is not a character device.
...
24: 4g /dev/ttyUSB2 does not exist or is not a character device.
25: 4g /dev/ttyUSB2 exists and is a character device.
```

> /dev/ttyUSB2 exists and is a character device. 
>
> 代表设备识别成功

或查看 /dev/ttyUSB2 是否存在

```
$ ls /dev/ttyUSB*
/dev/ttyUSB0  /dev/ttyUSB1  /dev/ttyUSB2  /dev/ttyUSB3  /dev/ttyUSB4
```



**判断是否拨号成功**

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

> 实际设备名称为 enx*，以实际为准



**ping网络测试**

```
(console)$ ping www.baidu.com -I enxca7f24fb0e94
PING www.wshifen.com (103.235.46.115) from 10.138.192.102 enxca7f24fb0e94: 56(84) bytes of data.
64 bytes from 103.235.46.115 (103.235.46.115): icmp_seq=1 ttl=45 time=1094 ms
64 bytes from 103.235.46.115 (103.235.46.115): icmp_seq=2 ttl=45 time=3924 ms
64 bytes from 103.235.46.115 (103.235.46.115): icmp_seq=3 ttl=45 time=1870 ms
```

> 指定设备 enx3e003a5bd6ad 进行ping网络测试



## GPU

```
ARM Mali G52 MC3
OpenGL ES 1.1, 2.0 and 3.2, OpenCL 2.1, Vulkan 1.2
```

**查看GPU使⽤率**

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

使用虚拟终端或调试串口终端，执行以下命令开始 GPU 性能测试

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

> 800x600 glmark2测试分数为  1405
>
> 测试结果仅做参考，实际分数以实际测量为准



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



## RKNN 部署

系统支持部署 rknn-toolkit2，rknn_model_zoo，参考 rockhip 官方文档如下：

https://github.com/airockchip/rknn-toolkit2/tree/master/doc

https://github.com/airockchip/rknn_model_zoo/tree/main/docs



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
>[  893.134037] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1333 us hw 1312 us
>[  893.167444] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1381 us hw 1313 us
>[  893.200503] rk_vcodec: 27b00100.rkvdec:0 session 3705:19 time: 1420 us hw 1313 us

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



## **GCC⼯具链**

**安装命令**

```
sudo apt update && sudo apt install -y build-essential manpages-dev
```



## 常见问题

### **Linux系统烧录后第一次开机重启问题**

通⽤Debian为了兼容不同芯⽚，`/etc/init.d/rockchip.sh` 第⼀次启动的时候，会根据芯⽚安装各种差异包，⽐如 `libmali` 、`isp`等packages，安装完后会重启显⽰服务。 如果是独⽴项⽬可以放到制作镜像的时候处理这部分差异即可。

