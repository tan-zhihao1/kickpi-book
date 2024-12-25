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

- 启动docker服务

```
$ sudo systemctl start docker
```

* Docker运行测试

```
$ sudo docker run hello-world
$ docker run -it ubuntu bash
```

