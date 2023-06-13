# 20-Ubuntu Custom





## 1. Autostart Programs Setup







## 2. QT Environment

### Development Environment Setup



### Runtime Environment Setup







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







