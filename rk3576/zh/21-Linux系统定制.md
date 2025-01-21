# 21-Linux系统定制



## 系统启动

### Linux系统登陆用户密码

Debian系统用户名/密码：linaro/linaro

Ubuntu系统用户名/密码：kickpi/kickpi



### 图形桌面登陆

图形桌面依赖显示设备，开机前需要先连接HDMI或LCD



### 调试串口登录

默认自动登录ROOT用户

ROOT用户默认未设置密码，可用以下命令配置ROOT密码

```
$ sudo passwd root
```



## SSH 登录

```
ssh <用户名>@<IP>
```

> 注意事项：
>
> 确保板子网络正常
>
> 确保能正常连通板子的IP
>
> 确保用户名和密码存在及正确

默认不支持root ssh 连接，root连接需要配置

```shell
$ vim /etc/ssh/ssh_config
+ PermitRootLogin yes
$ vim /etc/ssh/sshd_config
+ PermitRootLogin yes
$ sudo /etc/init.d/ssh restart
```

root登陆需要确认配置

```
$ cat /etc/ssh/ssh_config | grep PermitRootLogin
PermitRootLogin yes
$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin yes
```



## ADB 功能

```
adb shell
```

上传文件 1.txt 到主板 /data/ 下

```
adb push ./1.txt /data/
```

下载文件 2.txt 到PC本地当前目录

```
adb pull /data/2.txt .
```



## 屏幕显示方向配置

Debian12

xrandr 进行配置屏幕

```
# 获取使用帮助
xrandr -help
```



* 查看屏幕信息

```
root@linaro-alip:/# xrandr
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



* 旋转控制

>根据xrandr，已知目前显示设备 HDMI-1

对 HDMI-1 进行旋转设置
```
$ xrandr --output HDMI-1 --rotate normal			// 画面正常显示
$ xrandr --output HDMI-1 --rotate right				// 向右旋转，顺时针旋转90度
$ xrandr --output HDMI-1 --rotate left				// 向左旋转，顺时针旋转270度
$ xrandr --output HDMI-1 --rotate inverted			// 反转，顺时针旋转180度
```



* 触摸校准

```shell
//第一歩 安装工具
$ apt update
$ apt install xinput
$ apt install xinput-calibrator

//第二步 查看 Device 和 ID，可知 goodix-ts 设备id 为 12
$ xinput_calibrator --list 
Device "goodix-ts" id=11

//第三歩 重置转置矩阵和校准矩阵，这里非常关键，转置矩阵需要根据屏幕旋转，校准矩阵设置默认值即可
$ xinput set-prop $id --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 --type=float "Coordinate Transformation Matrix" 0 -1 1 1 0 0 0 0 1

$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
// xinput set-prop 11 --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0

//修改触摸方向（可根据名字修改） 默认方向
$ xinput set-prop $id 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1
 
//修改触摸方向（可根据名字修改） 向左90度
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1

//修改触摸方向（可根据名字修改） 向右90度
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
 
//修改触摸方向（可根据名字修改） 旋转180度
$ xinput set-prop $id 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
// xinput set-prop 11 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1

//第四步 开始校准
$ xinput_calibrator -v --device $id
// xinput_calibrator -v --device 11

```



## 语言配置

debian12

修改系统语言，请打开命令行终端，执行以下命令。重新启动使修改生效

* 设置英文语言

```
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" > /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" > /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
$ reboot
```

* 设置中文语言

```
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

### debian12

使用 `ifconfig` 和 `route` 命令（临时设置）

这种方法在系统重启后会失效，但可以快速测试固定 IP 的配置。

1. **使用 `ifconfig` 命令设置 IP 地址、子网掩码和广播地址**：

```
sudo ifconfig eth0 <IP 地址> netmask <子网掩码> broadcast <广播地址>
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
sudo route add default gw <网关地址> eth0
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



## 备份文件系统

当修改文件系统后，需要将其拷贝到其他相同板卡需求。

可通过下面方式导出文件系统，进行重新烧录。

接好U盘（或其他存储设备）在板子上，大小至少16GB以上，打包出来的镜像会比较大

在板子上运行我们的打包脚本

```
ff_export_rootfs (U盘路径) -t ext4
```

生成的包名格式

```
rootfs.img
```

> 注意：由于 FAT32 单个文件不超4G，不建议使用该格式文件系统存储设备进行备份

