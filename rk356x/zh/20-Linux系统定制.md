# 20-Linux系统定制





## 1. 开机自启动程序

### 开机自启动脚本

```
$ /etc/init.d/kickpi.sh
```

> 将开机启动程序命令添加到kickpi.sh脚本中即可实现开机自启动





## 2. 语言配置

修改系统语言，请打开命令行终端，执行以下命令。重新启动使修改生效

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

> 根据以上日志可知，当前系统为单屏幕显示，并且显示设备名称为HDMI-1
>
> 显示设备名称 用于指定旋转显示设备



* 旋转屏幕显示方向

```
$ xrandr --output HDMI-1 --rotate normal			// 旋转0度
$ xrandr --output HDMI-1 --rotate left				// 旋转90度
$ xrandr --output HDMI-1 --rotate inverted			// 旋转180度
$ xrandr --output HDMI-1 --rotate right				// 旋转270度
```



- 旋转屏幕后触摸校准

```
$ apt install xinput xinput_calibrator //安装工具
$ xinput_calibrator --list //查看Device和ID
	Device "goodix-ts" id=10
#第三歩 重置转置矩阵和校准矩阵，这里非常关键，转置矩阵需要根据屏幕旋转，校准矩阵设置默认值即可
$ xinput set-prop $id --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1
$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0

#修改触摸方向（可根据名字修改） 默认方向
xinput set-prop 10 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
 
#修改触摸方向（可根据名字修改） 向左90度
xinput set-prop 10 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
 
#修改触摸方向（可根据名字修改） 向右90度
xinput set-prop 10 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
 
#修改触摸方向（可根据名字修改） 旋转180度
xinput set-prop 10 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1


#第四步 开始校准
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



## 6. NFS配置

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
