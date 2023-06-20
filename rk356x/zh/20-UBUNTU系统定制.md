# 20-UBUNTU系统定制





## 1. 开机自启动程序





## 2. QT环境测试

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