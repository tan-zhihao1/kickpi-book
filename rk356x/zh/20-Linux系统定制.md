# 20-Linux系统定制



## 1. 开机自启动程序

### 开机自启动脚本

```
$ /etc/init.d/kickpi.sh
```

示例：

```diff
$ vim /etc/init.d/kickpi.sh
+ source /usr/bin/test_boot.sh
```

> 将开机启动程序命令添加到kickpi.sh脚本中，可实现开机自启动。



## 2. 语言配置

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



## 3. 时区配置

> 命令执行后，重启生效

* 设置时区亚洲/上海

```
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     
```

* 设置时区美国/纽约

```
$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```



## 4. 配置屏幕显示方向

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

设置 HDMI-1 逆时针旋转 90 度。

```
$ xrandr --output HDMI-1 --rotate left
```



- 旋转屏幕后触摸校准

安装工具

```
$ apt install xinput xinput_calibrator
```

查看Device和ID

```
$ xinput_calibrator --list
	Device "goodix-ts" id=10
```

> 根据打印信息可知，当前触摸设备为"goodix-ts"，id为10

设置输入设备的校准矩阵

```
$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
```

设置输入设备坐标转换矩阵

```
# 0°
xinput set-prop 10 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
# left 90°
xinput set-prop 10 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
# right 90°
xinput set-prop 10 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
# 180°
xinput set-prop 10 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
```
> 根据旋转情况使用对应命令

校准

```
$ /usr/bin/xinput_calibrator -v --device $DEVICE
```



## 5. 网络配置

### 静态IP地址配置

示例：ens33网卡配置静态IP地址为192.168.1.50

```
$ vim /etc/netplan/01-network-manager-all.yaml

network:
	version: 2
    ethernets:
        ens33:
            dhcp4: no
            addresses: [192.168.1.50/24]
            optional: true
            gateway4: 192.168.1.1
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



## 6. NFS配置

环境配置

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

   

## 7. 镜像源设置

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



## 8. Chrome 硬件加速测试

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



## 9.SSH配置

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



## 10.触摸屏 图像界面文件管理器打不开文件

1.打开桌面Home 进入文件管理器

2.右上角第二个选项edit >> Perferences... 

3.Perferences界面 第三页Navigation 选择 Single click to activate items



## 11.UBUNTU从官网安装软件包

安装gcc为例子

`wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb`
`sudo dpkg -i *.deb`
网站是https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html 可以直接搜索想要的依赖包名

![f78e90f7748d198d11dbbd163bb33a9](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f78e90f7748d198d11dbbd163bb33a9.png)



## 12 备份SD卡系统

> 注意：这种方式替换后的rootfs 可能存在mount挂载UUID问题 可以blkid查看正确的UUID 然后修改/etc/fstab

接好U盘在板子上，大小至少16GB以上，打包出来的镜像会比较大

在板子上运行我们的打包脚本

```
sudo ./ff_export_rootfs /mnt/usb -t ext4
```

> 生成的包名格式如：rootfs.img
>
> /mnt/usb 为U盘挂载的目录

对需要替换这个系统的板子单独烧录rootfs即可



## 13 Ubuntu桌面版进入root用户

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



