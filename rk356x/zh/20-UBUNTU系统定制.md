# 20-UBUNTU系统定制





## 1. 开机自启动程序





## 2. QT环境测试

```
```









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