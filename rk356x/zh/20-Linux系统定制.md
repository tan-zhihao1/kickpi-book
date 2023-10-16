# 20-Linux系统定制





## 1. 开机自启动程序

### 开机自启动脚本

```
$ /etc/init.d/kickpi.sh
```

> 将开机启动程序命令添加到kickpi.sh脚本中即可实现开机自启动





## 2. QT运行环境搭建

### Debian11

Debian11系统默认配置好QT运行环境，可执行以下命令测试

```
$ cd /usr/lib/aarch64-linux-gnu/qt5/examples/qpa/windows/
$ ./windows
```



* 打开双QT窗口界面

![a29f89e0967515369b000d08b2ff832](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/a29f89e0967515369b000d08b2ff832.jpg)





## 3. Docker运行环境搭建

### Debian11

* 安装Docker.io

```
$ sudo apt update
$ sudo apt install docker.io
```



* 防火墙配置

```
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```



* Docker运行测试

```
$ sudo docker run hello-world
$ docker run -it ubuntu bash
```





## 4. 语言配置

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



## 5. 时区配置

> 命令执行后，重启生效

* 设置时区亚洲/上海

```
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     
```

* 设置时区美国/纽约

```
$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```



## 6. 配置屏幕显示方向

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







