# 02-Linux常用系统定制



## Linux系统登陆用户密码

Ubuntu系统用户名/密码：kickpi/kickpi



## PIN 控制



### sys gpio控制

当存在将拓展引脚配置为输入的需求，默认软件的gpio-led不能满足需求。

将GPIO释放出来，通过/sys/class/gpio进行控制



步骤一

先将对应GPIO引脚注释，/sys/class/gpio/export 只能导入未注册的 gpio

比如PH8，如需其他引脚，一样在 leds 结点下注释即可 ,  下面内容都以PH8 做举例

```diff
vim device/config/chips/a133/configs/c3/kickpi-k5.dts
+ /*
			PH8 {
				label = "PH8";
				gpios = <&pio PH 8 1 0 1 0>;
				linux,default_trigger = "default-on";
				default-state = "on";
			};
+ */
```



步骤二

​		编译镜像，重新烧录

步骤三

​		确认gpio未被注册

```
root@kickpi:~# cat /sys/kernel/debug/pinctrl/pio/pinmux-pins  | grep PH8
pin 232 (PH8): (MUX UNCLAIMED) (GPIO UNCLAIMED)
```

​		能够看到PH8未被使用，且对应 pin 值为 232 



步骤四

​		通过 /sys/class/gpio/export 注册 PH8 并进行控制

```
// 注册
root@kickpi:~# echo  232 > /sys/class/gpio/export
// 查看是否生成
root@kickpi:~# ls /sys/class/gpio/
export  gpio232  gpiochip0  gpiochip352  unexport
// 注册后的结点内容
root@kickpi:~# ls /sys/class/gpio/gpio232
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

​			通过结点下的内容控制 gpio , 常用如下

```
direction
	in / out
	echo in > /sys/class/gpio/gpio232/direction
	echo out > /sys/class/gpio/gpio232/direction
value
	0 / 1
	cat /sys/class/gpio/gpio232/value 		// 读取
	echo 1 > /sys/class/gpio/gpio232/value	// 配置高电平
	echo 0 > /sys/class/gpio/gpio232/value  // 配置低电平
```



### sunxi gpio 控制

```
cd /sys/kernel/debug/sunxi_pinctrl

查看 pin 的配置
# echo PH8 > sunxi_pin
# cat sunxi_pin_configure

配置 pin 为 gpio 模式
# echo 'PH8 0' > function

修改 pin 的上拉属性
# echo 'PH8 1' > pull
// 查看修改情况
# cat pull					
# cat sunxi_pin_configure

查看 pin 电平
# cat data

注意：
当操作PL及之后的pin ， 需要切换pin的设备， 否则操作失败
echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
cat /sys/kernel/debug/sunxi_pinctrl/dev_name

echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
cat /sys/kernel/debug/sunxi_pinctrl/dev_name
```



## UBUNTU 1604

ubuntu 文件系统

```
device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



### chroot 方式修改 ubuntu

搭建构建环境

```
sudo apt-get install binfmt-support qemu-user-static
sudo dpkg -i ubuntu-build-service/packages/*
sudo apt-get install -f
```



修改前先备份保留原本的镜像

```
cp device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts_backup.tar.gz
```



解压

```
mkdir rootfs_k5
sudo tar -zxf device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz -C rootfs_k5
```



chroot 进行挂载，等同于在 root 下进行修改ubuntu镜像

```shell
// 配置以及挂载
$ ./ch-mount.sh -m rootfs_k5
$ sudo cp -b /etc/resolv.conf rootfs_k5/etc/resolv.conf
$ sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

// 通过chroot挂载修改
$ sudo chroot rootfs_k5
# export LC_ALL=C.UTF-8
# echo "nameserver 8.8.8.8" >> etc/resolv.conf
# echo "nameserver 114.114.114.114" >> etc/resolv.conf
// 测试是否有网
# ping www.baidu.com  
# apt-get update
# apt-get upgrade
// 下载需要的东西 或 修改文件
# apt install 		
// chroot下用exit退出
# exit

// 取消挂载
$ ./ch-mount.sh -u rootfs_k5
```



将修改后的ubuntu重新打包 **(注意：必须取消挂载！！！)**

```shell
rm -v device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
cd rootfs_k5/
sudo tar -zcf ../device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz .
cd ..
ls device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



重新编译即可 ./build.sh , 会将 新的 root fs 打包编译进镜像。

报错问题

![image-20241022183551403](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241022183551403.png)

修改ubuntu会影响镜像大小，mkfs.ubifs 会报错

需要修改 build/mkcmd.sh

```diff
vim build/mkcmd.sh
- fakeroot mkfs.ubifs -m 4096 -e 258048 -c 1280 -F -x zlib -r ${ROOTFS} -o ${LICHEE_PLAT_OUT}/rootfs.ubifs
+ fakeroot mkfs.ubifs -m 4096 -e 258048 -c 2560 -F -x zlib -r ${ROOTFS} -o ${LICHEE_PLAT_OUT}/rootfs.ubifs
```



注意：如果chroot修改方式不生效，则需要查看 overlay路径下文件是否存在覆盖文件!



### overlay 方式修改ubuntu

将需要替换的文件存放到overlay下，编译会拷贝替换 rootfs 对应路径下的文件

```
overlay
```



比如，需要将脚本放到 /etc/test.sh，则放到代码路径

```
overlay/etc/test.sh
```

编译会拷贝到根目录 /etc/test.sh



### 制作打包根文件系统

目的：将板子的修改的配置，导出根文件系统镜像，重新打包成烧录镜像

```bash
# ff_export_rootfs U盘或SD卡的路径
如：
# ff_export_rootfs /media/kickpi/EAA1-F4D4/	
	Export rootfs to /media/kickpi/EAA1-F4D4//rootfs.img Success
# ls /media/kickpi/EAA1-F4D4/
	rootfs.img
```



在已经编译过的代码上，将生成的 rootfs.img 重新打包

```bash
rm out/a133/c3/bsp/rootfs.ext4
cp (rootfs.img的路径) out/a133/c3/bsp/rootfs.ext4
./build.sh pack
```



## 分区修改

目前软件默认rootfs分区为 6G，除其他必要分区外，剩余空间分配给 UDISK 分区 `/dev/mmcblk0p6`

```
# fdisk  -l
Device            Start      End  Sectors  Size Type
/dev/mmcblk0p1    73728   139263    65536   32M Microsoft basic data
/dev/mmcblk0p2   139264   172031    32768   16M Microsoft basic data
/dev/mmcblk0p3   172032   303103   131072   64M Microsoft basic data
/dev/mmcblk0p4   303104 12886015 12582912    6G Microsoft basic data
/dev/mmcblk0p5 12886016 12918783    32768   16M Microsoft basic data
/dev/mmcblk0p6 12918784 15155166  2236383  1.1G Microsoft basic data
```

修改分区表

```diff
--- a/device/config/chips/a133/configs/default/sys_partition.fex
+++ b/device/config/chips/a133/configs/default/sys_partition.fex
@@ -53,7 +53,7 @@ size = 16384
 
 [partition]
     name         = rootfs
-    size         = 14931722
+    size         = 12582912
     downloadfile = "rootfs.fex"
     user_type    = 0x8000
```

默认未挂载 UDISK 分区，可手动将其挂载

```
// 默认无文件系统格式，将其格式化未 ext4
sudo mkfs.ext4 /dev/mmcblk0p6

// 挂载 UDISK 分区
mkdir /data
mount /dev/mmcblk0p6 /data
```

fstab 修改方式，系统启动会将其默认挂载

```
# vim /etc/fstab
/dev/mmcblk0p6  /data       ext4    defaults        0       0
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
$ mkdir /home/huangcm/nfs_share
$ chmod 777 /home/huangcm/nfs_share
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
/home/huangcm/nfs_share *(rw,sync,no_subtree_check,insecure)
```



2. 启动服务

```
sudo service nfs-kernel-server restart
```



3. 查看当前服务器共享文件，证明共享成果

```
$  showmount -e localhost
Export list for localhost:
/home/huangcm/nfs_share *
```



**客户端**

1. 查看服务器共享文件

```
showmount -e 192.168.199.173
Export list for 192.168.199.173:
/home/huangcm/nfs_share *
```



2. 挂载文件夹

```
$ mkdir nfs_tmp
$ sudo mount -t nfs 192.168.199.173:/home/huangcm/nfs_share nfs_tmp/
```



3. 挂载成功

```
$ ls nfs_tmp/
1.txt
```

   

## SSH配置

```
用户：kickpi
密码：kickpi
(su)root密码： kickpi
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

## WIFI配置

### WIFI AP热点

查看硬件支持

``` 
iw list | grep "AP"
```

安装工具

``` shell
apt-get update
apt-get install hostapd dnsmasq
```

配置静态IP

``` shell
vim /etc/network/interfaces

auto wlan0
iface wlan0 inet static
    address 192.168.42.1
    netmask 255.255.255.0
    
重启服务
systemctl restart networking
```

配置hostapd

``` shell
vim /etc/hostapd/hostapd.conf

##### hostapd configuration file ##############################################

interface=wlan0
ctrl_interface=/var/run/hostapd
ssid=MyHotspot
wpa_passphrase=88888888
channel=6
wpa=2
#bridge=br0

##### default configuration #######################################

#driver=rtl871xdrv
driver=nl80211
beacon_int=100
hw_mode=g
ieee80211n=1
wme_enabled=1
ht_capab=[SHORT-GI-20][SHORT-GI-40][HT40+]
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
max_num_sta=8
wpa_group_rekey=86400
```

``` shell
vim /etc/default/hostapd
DAEMON_CONF段修改为
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```

配置dnsmasq

``` shell
备份原文件
mv /etc/dnsmasq.conf /etc/dnsmasq.conf.bak
vim /etc/dnsmasq.conf

interface=wlan0
dhcp-range=192.168.42.2,192.168.42.254,255.255.255.0,24h
```

启动服务

``` shell
systemctl start hostapd dnsmasq
systemctl enable hostapd dnsmasq
```



## 关闭开机4G配置

开机会检测4G模块，自动拨号，关闭需要去掉

```diff
$ vim usr/lib/lbc/hardware-optimization 
4g_config &
- 4g_config &
```

## VNC远程桌面登陆

vnc软件下载

[Download VNC Viewer by RealVNC®](https://www.realvnc.com/en/connect/download/viewer/?lai_vid=EW3mEzvVmIeJO&lai_sr=10-14&lai_sl=l)

``` shell
板卡安装并开启VNC服务，并配置密码
apt-get install tightvncserver
su kickpi 使用kickpi用户，root用户下使用会出现无法显示导航栏的问题
vncserver -geometry 1920x1080
```

![image-20250516114106707](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250516114106707.png)

修改VNC配置

vim /home/kickpi/.vnc/xstartup

注释所有内容并添加以下内容

``` shell
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
```

![image-20250317142240671](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142240671.png)

``` shell
重启vnc 使配置生效
vncserver -kill :1
vncserver -geometry 1920x1080
```

输入ip端口号和密码

![image-20250317142654795](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142654795.png)

![image-20250516114712032](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250516114712032.png)

## 卸载xfce桌面

```
$ sudo apt-get remove xubuntu-desktop
$ sudo apt-get autoremove
$ sudo apt-get clean
```



## 禁止桌面环境，纯命令行启动

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



## 开机LOGO修改

可直接修改此文件

``` shell
/media/kickpi/Volumn/bootlogo.bmp
```

桌面双击Volumn即可直接将此文件所在分区挂载出来

![image-20250311194606624](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250311194606624.png)


## 添加USB转UART模块支持

> 由于不同的USB转UART模块存在不同ID，内核默认不一定包含

1. 接入USB转UART模块，查看新增的USB `PID`及`VID`

```
$ lsusb
Bus 003 Device 001: ID 1a86:7523  
```

2. 内核添加对应设备PID及VID

```diff
--- a/kernel/linux-4.9/drivers/usb/serial/option.c
+++ b/kernel/linux-4.9/drivers/usb/serial/option.c
@@ -1980,6 +1980,7 @@ static const struct usb_device_id option_ids[] = {
          { USB_DEVICE(0x2C7C, 0x0306) }, /* Quectel EG06/EP06/EM06 */
          { USB_DEVICE(0x2C7C, 0x0296) }, /* Quectel BG96 */
 #endif
+        { USB_DEVICE(0x1a86, 0x7523) }, /* 1a86:7523 QinHeng Electronics HL-340 UsB-serial adapter */
        /* fibocom L610 */
        { USB_DEVICE(FIBOCOM_VENDOR_ID, FIBOCOM_PRODUCT_L61031) },
        { USB_DEVICE(FIBOCOM_VENDOR_ID, FIBOCOM_PRODUCT_L61032) },
```

> 内核路径以实际为准

3. 重新编译，烧录软件

4. 接入USB转UART模块，查看新增的tty进行通信

```
$ ls /dev/tty*
```



## 其他事项

### xfce 浏览器打不开

需要安装火狐浏览器

```
sudo apt install firefox
```



### xrandr 无法旋转

a133 目前不支持 xrandr 进行旋转

