# 01-Linux常用系统定制



## Linux系统登陆用户密码

Ubuntu系统用户名/密码：kickpi/kickpi

ROOT密码：root



## Linux换默认登录用户

串口登录：/lib/systemd/system/serial-getty@.service.d/override.conf

![image-20250305115657169](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305115657169.png)

## 桌面环境切换为中文

[修改xfce桌面环境为中文](https://blog.csdn.net/weixin_51612528/article/details/147515617#:~:text=%E6%A3%80%E6%9F%A5%E5%BD%93%E5%89%8D%E8%AF%AD%E8%A8%80%E7%8E%AF%E5%A2%83%EF%BC%8C%E6%89%A7%E8%A1%8C%E6%8C%87%E4%BB%A4%20locale%EF%BC%8C%E5%A6%82%E6%9E%9C%E8%BE%93%E5%87%BA%E7%9A%84%20LANG%E3%80%81LC_ALL%20%E7%AD%89%E6%9C%AA%E5%8C%85%E5%90%AB%20zh_CN.UTF-8%EF%BC%8C%E9%9C%80%E8%A6%81%E8%AE%BE%E7%BD%AE%E4%B8%AD%E6%96%87%E7%8E%AF%E5%A2%83%E3%80%82%20sudo%20apt%20install,language-pack-zh-hans%20language-pack-zh-hant%20%E5%9C%A8%E5%BC%B9%E5%87%BA%E7%9A%84%E7%AA%97%E5%8F%A3%E4%B8%AD%E9%80%89%E6%8B%A9%20zh_CN.UTF-8%20UTF-8%20%E7%84%B6%E5%90%8E%E5%9B%9E%E8%BD%A6%E7%A1%AE%E8%AE%A4%E3%80%82%20%E7%84%B6%E5%90%8E%E7%BB%A7%E7%BB%AD%E9%80%89%E6%8B%A9%20zh_CN.UTF-8%EF%BC%8C%E5%9B%9E%E8%BD%A6%E7%A1%AE%E8%AE%A4%E3%80%82)





## 桌面环境修改为ROOT登录

修改/etc/lightdm/lightdm.conf.d/22-autologin.conf

``` shell
[Seat:*]
autologin-user=root  //修改为root
autologin-user-timeout=0
user-session=xfce

```

修改/etc/lightdm/users.conf

``` shell
[UserList]
minimum-uid=0 # 允许UID=0（即root）登录
hidden-users=nobody nobody4 noaccess
hidden-shells=/bin/false /usr/sbin/nologin /sbin/nologin
```

## 无桌面版本，终端登录

修改/usr/lib/systemd/system/getty@.service.d/override.conf

``` 
[Service]
ExecStartPre=/bin/sh -c 'exec /bin/sleep 10'
ExecStart=
ExecStart=-/sbin/agetty --noissue --autologin root %I $TERM
Type=idle
```



## 修改默认USER_KEY功能

桌面版按下USER按键是打开login out界面

设置里取消功能即可

![image-20250818163503951](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250818163503951.png)

![image-20250818163514774](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250818163514774.png)

服务器版本按下USER按键是直接关机

取消这个功能方法如下

**修改systemd的登录器配置**

``` 
sudo nano /etc/systemd/logind.conf
```

**取消注释并修改以下行**

``` 
HandlePowerKey=ignore
HandleSuspendKey=ignore
HandleHibernateKey=ignore
```

**重启服务**

``` 
sudo systemctl restart systemd-logind
```

修改完成后就能正常让应用获取按键的上报事件

![image-20250526140216634](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250526140216634.png)

## 静态IP配置

``` shell
vim /etc/network/interfaces
添加以下内容

auto eth0
iface eth0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1

根据自己的网关和网段进行配置，否则无法访问网络

```



## 自动烧录

下面命令编译固件制作的SD卡为烧录卡

```
$ ./build.sh auto_burn=true
```

> 烧录卡想要取消自动烧录 可以通过板子SD烧录卡启动后输入系统下面命令控制
>
> 禁用自动烧录服务

```
$ sudo systemctl disable kickpi.service 
```

> 启动自动烧录服务（烧录卡默认启动）

```
$ sudo systemctl enable kickpi.service 
```



## 拷贝eMMC系统到新SD卡（系统备份）

准备一张高速SD卡 建议class10的，SD卡性能会影响系统备份时间

将SD卡插入卡槽

lsblk

![image-20250304192434948](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250304192434948.png)

格式化SD卡

``` shell
mkfs.ext4 /dev/mmcblk1
```

![image-20250304192605788](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250304192605788.png)

``` shell
dd if=/dev/mmcblk0  of=/dev/mmcblk1  bs=4M
```

开始拷贝，需耐心等待，根据SD卡性能大概需要10~25分钟。

![image-20250304192922929](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250304192922929.png)

请耐心等待拷贝完成

![image-20250304195232800](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250304195232800.png)

拷贝完成

## 拷贝eMMC系统生成系统镜像

准备一张具有32G空闲容量的U盘，U盘性能会影响系统备份时间

挂载U盘

``` shell
mkdir /mnt/sda
mount /dev/sda1 /mnt/sda
```

拷贝生成系统镜像

``` shell
dd if=/dev/mmcblk0  of=/mnt/sda/output.img  bs=4M status=progress
sync /mnt/sda/
```

拷贝完成后生成的镜像大小会是30G左右

将镜像文件拿到虚拟机ubuntu环境下

使用以下命令稀释镜像文件

``` shell
img2simg output.img h618.img
```

![image-20250618111201936](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250618111201936.png)

得到的h618.img即可用于烧录

## 备份SD卡系统

这种方式替换后的rootfs存在mount挂载UUID问题 可以blkid查看正确的UUID 然后修改/etc/fstab 或者在替换前删除此文件

### 操作步骤

接好U盘，大小至少16GB以上，打包出来的镜像会比较大

* U盘挂载出来：

```shell
$ sudo mkdir -p /mnt/usb
$ sudo mount /dev/sda1 /mnt/usb
```

* 脚本所在网盘路径：

```
h618_data\3-SoftwareData软件资料\Linux打包rootfs脚本\ff_export_rootfs
```

* 在板子上运行我们的打包脚本

```shell
$ sudo ./ff_export_rootfs /mnt/usb/ -t ext4
```

> 生成的包名格式如：Ubuntu22.04.5LTS_ztl_ext4_202411131114.img

等待打包结束，时间较长

> 如果需要对img进行再进一步的修改，可以通过下面方式 在虚拟机中挂载出img里面的内容

````shell
$ sudo mkdir test_rootfs/
$ sudo mount ./Ubuntu22.04.5LTS_ztl_ext4_202411131114.img  ./test_rootfs/
````

### AW-IMAGE SDK替换步骤

AW-IMAGE SDK替换指定rootfs.img说明

```diff
$ vim scripts/debootstrap.sh

@@ -612,6 +612,12 @@ create_image()
 
        # stage: write u-boot
        write_uboot $LOOP
+       
+		# 保留boot分区、删除原ROOTFS内容
+       mv $MOUNT/boot $MOUNT/../boot
+       rm $MOUNT/* -rf
+
+		# 替换自定义ROOTFS,以下示例为解压tar包,其他替换方式可自行修改
+       tar -xf ${BUILD_DIR}/rootfs-base/stage3-arm64-openrc-20240707T225101Z.tar -C $MOUNT/
+
+		# 回溯boot分区
+       mv $MOUNT/../boot $MOUNT/boot
 
        # fix wrong / permissions
        chmod 755 $MOUNT
        
```

或参考其他客户成功的案例修改方式：

![aefb737ab69097882aedfad9c2fdb27](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/aefb737ab69097882aedfad9c2fdb27.png)

## 设置开机自启动命令

把需要的命令写到系统里面的/etc/rc.local

或者在/etc/systemd/system/下写服务文件启动脚本

## 设置wifi热点（AP）模式

查看是否支持AP模式

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

> AP/VLAN则可以表示硬件支持



```
#创建依赖
sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged 

# 创建虚拟网卡
sudo iw dev <wirelessname> interface add <virtualwlanname> type __ap  
# <wirelessname> 是真实无线网卡名，可通过ifconfig查看，<virtualwlanname>是虚拟的无线网卡名
#例如命令 
sudo iw dev wlan0 interface add wlo2 type __ap

#为虚拟网卡添加物理地址
sudo ip link set dev <virtualwlanname> address 22:33:44:55:66:00
# 随意填写，假如冲突则换一个，<virtualwlanname>是虚拟的无线网卡名
#例如命令：
sudo ip link set dev wlo2 address 22:33:44:55:66:00
```



```
#查看创建情况
sudo iw dev <virtualwlanname> info
sudo iw dev wlo2 info

# 输出内容类似
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

> 注意：重启电脑后，这里创建的虚拟网卡就会失效
>

1. 下载安装工具 create_ap

```
git clone https://github.com/oblique/create_ap
cd */create_ap
sudo make install
```

2. 使用create_ap创建热点



```
sudo create_ap -c 11 <virtualwlanname> <wirelessname> <SSID> <password> 
#<wirelessname> 是你的无线网卡的姓名，<virtualwlanname> 虚拟网卡名，<SSID> <password>分别是创建的热点wifi名和密码

#例如 
sudo create_ap -c 11 wlo2 wlan0 m3 88888888

```

3. 如果创建的热点卡住

```
开启热点时报如下错误:
#RTNETLINK answers: Device or resource busy

#ERROR: Maybe your WiFi adapter does not fully support virtual interfaces.
     #  Try again with --no-virt.
     
可以如下操作停止之前创建的热点，然后重启开启热点。
sudo create_ap --stop <virtualwlanname>  #<virtualwlanname> 虚拟网卡名
```





### 连接WiFi并获取ip

```
touch /etc/wpa_supplicant.conf
vi /etc/wpa_supplicant.conf
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
update_config=1

network={
        ssid="wifi_oranth_2.4"
        psk="123456789"
        key_mgmt=WPA-PSK
}

命令连接网络 获取ip
wpa_supplicant -D nl80211 -i wlan0 -c /var/run/wpa_supplicant -B
dhclient wlan0
```

### 设置开机自动连接wifi并获取ip

``` 
vi /etc/network/interfaces
auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant.conf
```



## VNC远程桌面登陆

vnc软件下载

[Download VNC Viewer by RealVNC®](https://www.realvnc.com/en/connect/download/viewer/?lai_vid=EW3mEzvVmIeJO&lai_sr=10-14&lai_sl=l)

``` shell
板卡开启VNC服务，并配置密码
vncserver
```

![image-20250317141556644](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317141556644.png)

修改VNC配置

vim /home/kickpi/.vnc/xstartup

注释所有内容并添加一下内容

``` shell
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
```

![image-20250317142240671](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142240671.png)

重启VNC服务

``` shell
vncserver -geometry 1920x1080
```

![image-20250317142417293](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142417293.png)

打开RealVNC Viewer

输入ip端口号和密码

![image-20250317142654795](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142654795.png)

![image-20250317142740629](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142740629.png)

## UBUNTU从官网安装软件包

安装gcc为例子

`wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb`
`sudo dpkg -i *.deb`
网站是https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html 可以直接搜索想要的依赖包名

![f78e90f7748d198d11dbbd163bb33a9](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f78e90f7748d198d11dbbd163bb33a9.png)



## 常见问题

**桌面声卡设置打开报错**

![05f3817bf038d6f1f138a290c0496e68](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/05f3817bf038d6f1f138a290c0496e68.jpg)

解决方法：

终端输入

``` shell
cd /bin
sudo ln -s pavucontrol pavucontrol-qt
sudo apt-get update
sudo apt purge pulseaudio
sudo apt install pulseaudio
```


## 

**有线网口使用局域网，WiFi连接网络时无法访问外网**

[解决Linux下同时使用有线和无线网络时，网络连接的优先级问题_linux以太网和无线网冲突问题-CSDN博客](https://blog.csdn.net/qq547276542/article/details/78713893)

![image-20250427165431777](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250427165431777.png)



