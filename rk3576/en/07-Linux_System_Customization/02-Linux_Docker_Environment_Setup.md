# 02-Linux_Docker_Environment_Setup

Test platform: K7 (RK3567)

The content of this document applies to the firmware updated after April 1, 2025.

## Ubuntu 24.04

* Firewall configuration

```shell
$ sudo apt install iptables
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

* Check if iptables is configured successfully

```shell
$ iptables --version
iptables v1.8.10 (legacy)
```

- Add Docker's official GPG key and APT source

```shell
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

* For machines in China, you can use the domestic source from [TUNA Mirror of Tsinghua University](https://mirrors.tuna.tsinghua.edu.cn/):

```shell
$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o  /usr/share/keyrings/docker-ce.gpg
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu $(lsb_release -sc) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

* Update the source

```shell
$ sudo apt update
```

* Install Docker

```shell
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

* Add the configuration file

```shell
$ sudo vim /etc/docker/daemon.json
{
  "iptables": false
}
```

* Start the service

```shell
$ sudo systemctl start docker
```

* If there are still issues, you can debug using the following command

```shell
$ sudo dockerd --debug
```

* Run tests

```shell
$ sudo docker run hello-world
$ sudo docker run -it debian bash
```

## Debian 12

* Firewall configuration

```shell
$ sudo apt install iptables
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

* Check if iptables is configured successfully

```shell
$ iptables --version
iptables v1.8.10 (legacy)
```

* Add Docker's GPG public key and apt source:

```
curl -sSL https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://download.docker.com/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list
```

* For machines in China, you can use the domestic source from [TUNA Mirror of Tsinghua University](https://mirrors.tuna.tsinghua.edu.cn/):

```shell
curl -sS https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list
```

* Update the source

```shell
$ sudo apt update
```

* Install Docker

```shell
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

* Add the configuration file

```shell
$ sudo vim /etc/docker/daemon.json
{
  "iptables": false
}
```

* Start the service

```shell
$ sudo systemctl start docker
```

* If there are still issues, you can debug using the following command

```shell
$ sudo dockerd --debug
```

* Run tests

```shell
$ sudo docker run hello-world
$ sudo docker run -it debian bash
```
