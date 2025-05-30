# 03-Linux_Docker



### Debian 11

* Install Docker.io

```bash
$ sudo apt update
$ sudo apt install docker.io
```

* Firewall Configuration

```bash
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

* Detect the docker service and start running without starting it.

```shell
$ sudo systemctl status docker
$ sudo systemctl start docker
```

* Docker Run Test

```bash
$ sudo docker run hello-world
$ docker run -it ubuntu bash
```

Make sure to run these commands with appropriate permissions, and you might need to use `sudo` for certain Docker commands based on your user permissions.
