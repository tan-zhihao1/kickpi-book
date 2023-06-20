# 20-Linux Custom







## 1. Startup Program Autostart

### Autostart Script

```
$ /etc/init.d/kickpi.sh
```

> Add the startup program command to the kickpi.sh script to enable autostart on boot





## 2. QT Runtime Environment Setup

### Debian11

Debian 11 comes with the QT runtime environment preconfigured. You can test it by executing the following commands

```
$ cd /usr/lib/aarch64-linux-gnu/qt5/examples/qpa/windows/
$ ./windows
```



* Open a dual QT window interface

![a29f89e0967515369b000d08b2ff832](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/a29f89e0967515369b000d08b2ff832.jpg)





## 3. Docker Environment Setup

### Debian11

* Installing Docker.io

```
$ sudo apt update
$ sudo apt install docker.io
```



* Firewall Configuration

```
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```



* Testing Docker

```
$ sudo docker run hello-world
$ docker run -it ubuntu bash
```





## 4. Language

To change the system language, open the command line terminal and execute the following commands.

PS: Restart for the changes to take effect.

### English Language

```
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
```



### China Language

```
$ locale-gen zh_CN.UTF-8
$ sed -i 's/^# *\(zh_CN.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=zh_CN.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
```







