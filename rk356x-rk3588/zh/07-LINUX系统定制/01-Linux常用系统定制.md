# 01-Linux常用系统定制



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 开机自启动程序

### 开机自启动脚本

```
$ /etc/init.d/kickpi.sh
```

示例：

```diff
$ vim /etc/init.d/kickpi.sh
+ source /usr/bin/test_boot.sh
```

给脚本赋予运行权限

```
$ chmod +x /etc/init.d/kickpi.sh
```

> 将开机启动程序命令添加到kickpi.sh脚本中，可实现开机自启动。



## 修改串口自动登录用户

默认串口自动登陆root，下面示例为修改为kickpi

```
$ vim /lib/systemd/system/serial-getty@.service
-ExecStart=-/sbin/agetty --autologin root --keep-baud 115200,38400,9600 %I $TERM
+ExecStart=-/sbin/agetty --autologin kickpi --keep-baud 115200,38400,9600 %I $TERM
```



## 语言配置

> 命令执行后，重启生效

### Ubuntu2004

* 设置英文语言

```
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
```

* 设置中文语言

```
$ locale-gen zh_CN.UTF-8
$ sed -i 's/^# *\(zh_CN.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=zh_CN.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
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



## 配置屏幕显示方向

* 查看当前屏幕信息

```
$ xrandr

Screen 0: minimum 320 x 200, current 1920 x 1080, maximum 16384 x 16384
HDMI-1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
   1920x1080     60.00*+  60.00    50.00    59.94    30.00    24.00    29.97    23.98  
   1920x1080i    60.00    50.00    50.00    59.94  
   1280x1024     60.02  
   1440x900      59.90  
   1360x768      60.02  
   1280x720      60.00    50.00    50.00    59.94  
   1024x768      60.00  
   800x600       60.32  
   720x576       50.00    50.00  
   720x576i      50.00  
   720x480       60.00    60.00    59.94    59.94  
   720x480i      60.00    59.94  
   640x480       60.00    59.94    59.94  
```

> 根据打印信息可知，当前系统为单屏幕显示，并且显示设备名称为 HDMI-1



* 旋转屏幕显示方向

设置指定显示设备的旋转方向

```
$ xrandr --output (dev) --rotate [normal|left|inverted|right]	
```

>normal：将显示设备正常的显示。
>
>left：将显示设备逆时针旋转 90 度。
>
>inverted：将显示设备顺时针旋转 90 度。
>
>right：将显示设备旋转 180 度。

示例：

配置 HDMI-1 显示逆时针旋转 90 度。

```
$ xrandr --output HDMI-1 --rotate left
```



- 旋转屏幕后触摸校准

安装工具

```
$ apt update
$ apt install xinput
$ apt install xinput-calibrator
```

查看设备和ID

```
$ xinput_calibrator --list
	Device "goodix-ts" id=10
```

> 根据打印信息可知，当前触摸设备为"goodix-ts"，id为10

配置输入设备的校准矩阵

```
$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
```

> 示例：
>
> 设置 goodix-ts 设备的校准矩阵
>
> $ xinput set-prop 10 --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0

配置输入设备坐标转换矩阵

```
// normal
$ xinput set-prop $id 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1 
// left
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
// right
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
// inverted
$ xinput set-prop $id 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
```

> 根据旋转情况使用对应命令
>
> 示例：
>
> 设置 goodix-ts 设备的坐标转换矩阵为left
>
> xinput set-prop 10 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1

校准触摸

```
$ xinput_calibrator -v --device $id
```

> 示例：
>
> 校准 goodix-ts 设备
>
> $ xinput_calibrator -v --device 10



### 永久修改触摸方向

可以参考LCD配置文档：[单触摸屏驱动配置](..\05-外设驱动\LCD.md#TouchDriver)



##  网络配置

### 静态IP地址配置

示例1：eth0网卡通过netplan的yaml配置静态IP地址为192.168.1.50

```shell
$ vim /etc/netplan/01-network-manager-all.yaml

network:
	version: 2
    ethernets:
        eth0:
            dhcp4: no
            addresses: [192.168.1.50/24]
            optional: true
            gateway4: 192.168.1.1
```

示例2：eth1网卡通过network/interfaces配置静态IP地址为192.168.77.196

```shell
$ sudo vim /etc/network/interfaces.d/eth1.cfg
auto eth1
iface eth1 inet static
address 192.168.77.196
netmask 255.255.255.0
gateway 192.168.77.1
dns-nameservers 8.8.4.4 8.8.8.8
```

重启服务

```shell
$ reboot
```



### 常用配置

**显示所有网络接口信息**

```
$ ifconfig -a
enP3p49s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 82:76:b8:2f:96:6d  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 168  base 0xd000  

enP4p65s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 6a:cb:0c:a5:15:e0  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 179  base 0x1000  
...
```

>网络接口名称根据实际情况会有所不同



**显示特定网络接口信息**

```
$ ifconfig <dev>
```

>要显示特定网络接口（如`eth0`）的信息，可输入`ifconfig eth0`



**设置 IP 地址**

```
$ ifconfig eth0 <ip> netmask <nmask>
```

> 要为`eth0`接口设置 IP 地址为`192.168.1.101`，子网掩码为`255.255.255.0`，可以使用命令`ifconfig eth0 192.168.1.101 netmask 255.255.255.0`



**启用网络接口**

```
$ ifconfig <dev> up
```

> 若要启用`eth0`接口，可以使用`ifconfig eth0 up`命令



**禁用网络接口**

```
$ ifconfig <dev> down
```

> 要禁用`eth0`接口，使用`ifconfig eth0 down`命令



**设置 MAC 地址**

```
$ ifconfig <dev> down
$ ifconfig <dev> hw ether <新的MAC地址>
$ ifconfig <dev> up
```

> 不同的操作系统可能会有一些差异，有些系统可能不允许通过`ifconfig`直接设置 MAC 地址，或者需要额外的步骤



## 命令行配置WIFI热点（AP）模式

**查看是否支持AP模式**

```shell
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

> AP/VLAN则可以表示硬件支持

### 创建虚拟接口创建热点模式

安装依赖包

```shell
$ sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged make
```

创建虚拟网卡

```shell
$ sudo iw dev <wirelessname> interface add <virtualwlanname> type __ap  
```

> <wirelessname> 是真实无线网卡名，可通过ifconfig查看，<virtualwlanname>是虚拟的无线网卡名

例如命令 

```shell
$ sudo iw dev wlan0 interface add wlo2 type __ap
```

为虚拟网卡添加物理地址

```shell
$ sudo ip link set dev <virtualwlanname> address 22:33:44:55:66:00
```

> 随意填写，假如冲突则换一个，<virtualwlanname>是虚拟的无线网卡名

例如命令：

```shell
$ sudo ip link set dev wlo2 address 22:33:44:55:66:00
```

查看创建情况

```shell
$ sudo iw dev wlo2 info
   Interface wlo2
	ifindex 5
	wdev 0x5
	addr 22:33:44:55:66:00
	type managed
	wiphy 0
	txpower 0.00 dBm
	multicast TXQ:
		qsz-byt	qsz-pkt	flows	drops	marks	overlmt	hashcol	tx-bytestx-packets
		0	0	0	0	0	0	0	0	0
```

> 注意：重启电脑后，这里创建的虚拟网卡就会失效



下载安装工具 create_ap

```shell
$ git clone https://github.com/oblique/create_ap
$ cd */create_ap
$ sudo make install
```

使用create_ap创建热点

```shell
$ sudo create_ap -c 11 <virtualwlanname> <wirelessname> <SSID> <password> 
```

>  <wirelessname> 是你的无线网卡的姓名，<virtualwlanname> 虚拟网卡名，<SSID> <password>分别是创建的热点wifi名和密码

例如 

```shell
$ sudo create_ap -c 11 wlo2 wlan0 m3 88888888
```

如果创建的热点卡住，开启热点时报如下错误:

```
#RTNETLINK answers: Device or resource busy

#ERROR: Maybe your WiFi adapter does not fully support virtual interfaces.
     #  Try again with --no-virt.
```

可以如下操作停止之前创建的热点，然后重启开启热点。

```shell
$ sudo create_ap --stop <virtualwlanname>  
```

> <virtualwlanname> 虚拟网卡名

### 使用物理接口创建热点模式

安装依赖和create_ap

```shell
$ sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged  make
$ git clone https://github.com/oblique/create_ap
$ cd */create_ap
$ sudo make install
```

修改防火墙

```shell
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
$ iptables --version
```

创建热点MyAccessPoint，密码：12345678，共享eth0网络

```shell
$ sudo create_ap --no-virt wlan0 eth0 MyAccessPoint 12345678 &
```



## NFS配置

环境配置

```
sudo apt install nfs-kernel-server
sudo apt install rpcbind
sudo apt install nfs-common
```

**服务器端**

1. 配置共享的文件

```diff
$ mkdir /home/kickpi/nfs_share
$ chmod 777 /home/kickpi/nfs_share
$ vi /etc/exports
+ /home/kickpi/nfs_share *(rw,sync,no_subtree_check,insecure)
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

客户端

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

   

## 镜像源设置

查看软件版本，根据版本查找镜像源替换

```
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.6 LTS
Release:        20.04
Codename:       focal
```



将新的镜像源替换并更新

```
$ sudo vi /etc/apt/sources.list
$ sudo apt update
```

注意：使用arm的镜像源



## Chrome 硬件加速测试

测试平台：RK3568 Debian11 Chrome

#### 测试方法：

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

#### 测试结果：

​	1080P_30帧/秒：GPU占用在42%-48%左右

​	每帧解码时间测试结果日志片段：

```
[  639.172225] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 4195 us hw 4095 us
[  639.180185] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 12116 us hw 7960 us
[  639.190246] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 19541 us hw 10027 us
[  639.199403] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 22900 us hw 9153 us
[  639.203403] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 20805 us hw 4053 us
[  639.213146] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 21496 us hw 9638 us
[  639.222129] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 21700 us hw 9056 us
[  639.228897] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 24810 us hw 6819 us
[  639.403439] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 6511 us hw 6482 us
[  639.411955] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 11883 us hw 8450 us
[  639.420139] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 17932 us hw 8009 us
[  639.429258] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 24691 us hw 9296 us
[  639.503067] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 5224 us hw 5164 us
[  639.509281] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 8586 us hw 6174 us
[  639.518009] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 14440 us hw 8704 us
[  639.529122] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 22595 us hw 11125 us
```

​	4K_30帧/秒：GPU占用在60%-68%左右

​	每帧解码时间测试结果日志片段：

```
[  938.649847] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 93967 us hw 27794 us
[  938.683932] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 93243 us hw 34114 us
[  938.717400] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 93712 us hw 33463 us
[  938.751306] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 100528 us hw 33952 us
[  938.780302] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 95406 us hw 28932 us
[  938.813718] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 94942 us hw 33414 us
[  938.838935] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 86479 us hw 25243 us
[  938.872539] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 91289 us hw 33486 us
[  938.906450] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 91721 us hw 33970 us
[  938.942726] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 102481 us hw 36284 us
[  938.960331] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 86302 us hw 17609 us
[  938.994526] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 86494 us hw 34091 us
[  939.025353] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 80775 us hw 30825 us
[  939.058137] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 96464 us hw 32684 us

```



## SSH配置

```
用户：kickpi
密码：kickpi
root密码修改： 
$ sudo passwd root
kickpi
kickpi
```

默认不支持root连接，root连接需要配置

```shell
$ vim /etc/ssh/ssh_config
+ PermitRootLogin yes
$ vim /etc/ssh/sshd_config
+ PermitRootLogin yes
$ sudo /etc/init.d/ssh restart
```

注意事项：

确保板子IP正常

确保能正常连通板子的IP

```
$ cat /etc/ssh/ssh_config | grep PermitRootLogin
PermitRootLogin yes
$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin yes
```



## USB 摄像头

**检查摄像头设备节点**

```
$ ls /dev/video*
/dev/video-dec0  /dev/video-enc0  /dev/video0  /dev/video1  /dev/video2
```

**ffmpeg 打开摄像头**

```
$ sudo apt install ffmpeg 
$ ffplay -f v4l2 -i /dev/video* 
```

示例：

```
$ ffplay -f v4l2 -i /dev/video1
```



## 触摸屏 图像界面文件管理器打不开文件

1.打开桌面Home 进入文件管理器

2.右上角第二个选项edit >> Perferences... 

3.Perferences界面 第三页Navigation 选择 Single click to activate items



## UBUNTU从官网安装软件包

安装gcc为例子

`wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb`
`sudo dpkg -i *.deb`
网站是https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html 可以直接搜索想要的依赖包名

![f78e90f7748d198d11dbbd163bb33a9](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f78e90f7748d198d11dbbd163bb33a9.png)



## 备份rootfs系统<a id='backup_rootfs'> </a>

> 注意：这种方式替换后的rootfs 可能存在mount挂载UUID问题 可以blkid查看正确的UUID 然后修改/etc/fstab

接好U盘在板子上，大小至少16GB以上，打包出来的镜像会比较大

脚本位于网盘：

```
rk356x-rk3588_data\3-SoftwareData\Linux_Backup_ROOTFS_Script
```

将脚本拷贝到板子Linux系统上运行

```shell
$ sudo chmod +x ./ff_export_rootfs
$ sudo ./ff_export_rootfs /mnt/usb -t ext4
```

> 生成的包名格式如：rootfs.img
>
> /mnt/usb 为U盘挂载的目录

确保打包出来的文件格式正确

```shell
$ file Ubuntu_24.04.2_LTS_ext4_202503062020.img 
Ubuntu_24.04.2_LTS_ext4_202503062020.img: Linux rev 1.0 ext4 filesystem data, UUID=71584b93-ad99-452e-a8e2-6b9ed76eff7d, volume name "rootfs" (extents) (64bit) (large files) (huge files)
```

> FAT32单文件不可超出4G，如果系统太大，请格式化U盘为其他格式，推荐：ext4、exfat

重新打包，将rootfs.img替换进完整镜像，参考[固件解包和打包](../08-进阶/03-固件解包和打包.md)

也可以对需要替换这个系统的板子单独烧录rootfs.img



## eMMC分区

重新分区均需要重新烧录，如果系统不想被格式化，请先[备份rootfs](#backup_rootfs)

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

* 完整镜像修改

参考[固件解包和打包](../08-进阶/03-固件解包和打包.md)，修改parameter.txt文件后重新打包

* SDK内修改

修改对应板子型号路径下文件，后重新编译

RK356x：

```
SDK$ device/rockchip/rk3566_rk3568/parameter-buildroot-fit.txt
```

RK3588：

```
SDK$ device/rockchip/rk3588/parameter.txt 
```



## Ubuntu桌面版进入root用户

最新桌面版系统默认登录用户为：kickpi

桌面版系统的Terminal 使用一些命令可能存在权限问题如：ifconfig

就需要root权限 可以在命令前面加sudo 或者 进入root用户

切换root用户操作如下：

1.设置root密码

```
$ sudo passwd root
```

提示输入当前用户密码 如：kickpi
提示 New Password： 即可输入你要设置的root密码 （输入不会显示）

提示 Retype new password: 再次确认

提示 passwd: password updated successfully 表示成功

2切换到root用户

```
$ su
```

 输入刚刚设置的密码 即可让当前Terminal 进入root用户



## 进入Linux命令行界面

### Ubuntu系统

​	关闭Ubuntu图形化桌面会进入Linux命令行界面，下面介绍怎么开关Ubuntu图形化桌面

暂时关闭桌面

```shell
$ sudo systemctl stop slim.service
```

临时启动桌面

```shell
$ sudo systemctl start slim.service
```

永久禁用桌面

```shell
$ sudo systemctl disable slim.service
```

永久启动桌面

```shell
$ sudo systemctl enable slim.service
```

GUI 桌面被冻结可以用键盘进入命令行界面

* 进入命令行界面： 输入快捷键  CTRL + ALT + F3。

* 要返回 GUI 桌面：输入快捷键  ALT + F2 。

### Debian系统

#### 禁止桌面环境，纯命令行启动

修改默认的运行模式

```shell
$ systemctl set-default multi-user.target
```

查看当前系统运行模式

```shell
$ systemctl get-default
```

重启

```shell
$ reboot
```

#### 启用桌面环境

修改默认的运行模式

```shell
$ systemctl set-default graphical.target
```

重启

```shell
$ reboot
```

