# 32-Linux Docker环境搭建

测试平台：K7（RK3567）



## Ubuntu 24.04

* 防火墙配置

```shell
$ sudo apt install iptables
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

* 检查iptables是否配置成功

```shell
$ iptables --version
iptables v1.8.10 (legacy)
```

- 添加 Docker 的官方 GPG 密钥和 APT 源

```shell
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

* 国内机器可以用[清华 TUNA](https://mirrors.tuna.tsinghua.edu.cn/) 的国内源：

```shell
$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o  /usr/share/keyrings/docker-ce.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu $(lsb_release -sc) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

* 更新源

```shell
$ sudo apt update
```

* 安装docker

```shell
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

* 添加配置文件

```shell
$ sudo vim /etc/docker/daemon.json
{
  "iptables": false
}
```

* 启动服务

```shell
$ sudo systemctl start docker
```

* 若还有问题可以通过下面命令进行debug

```shell
$ sudo dockerd --debug
```

* 运行测试

```shell
$ sudo docker run hello-world
$ sudo docker run -it debian bash
```



## Debian12

* 防火墙配置

```shell
$ sudo apt install iptables
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

* 检查iptables是否配置成功

```shell
$ iptables --version
iptables v1.8.10 (legacy)
```


*  添加Docker 的 GPG 公钥和 apt 源：

```
curl -sSL https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://download.docker.com/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list

```

* 国内机器可以用[清华 TUNA](https://mirrors.tuna.tsinghua.edu.cn/) 的国内源：

```shell
curl -sS https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list

```

* 更新源

```shell
$ sudo apt update
```

* 安装docker

```shell
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

* 添加配置文件

```shell
$ sudo vim /etc/docker/daemon.json
{
  "iptables": false
}
```

* 启动服务

```shell
$ sudo systemctl start docker
```

* 若还有问题可以通过下面命令进行debug

```shell
$ sudo dockerd --debug
```

* 运行测试

```shell
$ sudo docker run hello-world
$ sudo docker run -it debian bash
```

