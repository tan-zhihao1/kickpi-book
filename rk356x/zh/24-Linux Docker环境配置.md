# 24-Linux Docker环境搭建





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

- 检测docker服务 没有启动就启动运行

```
$ sudo systemctl status docker
$ sudo systemctl start docker
```

* Docker运行测试

```
$ sudo docker run hello-world
$ docker run -it ubuntu bash
```

