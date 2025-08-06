# 01-Linux常用系统定制

## Ubuntu文件系统修改

**chroot 方式修改 ubuntu**

搭建构建环境

```shell
sudo apt-get install qemu-user-static
```

修改前先备份保留原本的镜像

```shell
cp device/config/rootfs_tar/ubuntu-armhf.tar.gz device/config/rootfs_tar/ubuntu-armhf.tar.gz-backup
```

解压

```shell
cd device/config/rootfs_tar/
mkdir rootfs_k4b
tar -zxf ubuntu-armhf.tar.gz -C rootfs_k4b
```

chroot 进行挂载，等同于在 root 下进行修改ubuntu镜像

```shell
// 配置以及挂载
$ ./ch-mount.sh -m rootfs_k4b
$ sudo cp -b /etc/resolv.conf rootfs_k4b/etc/resolv.conf
$ sudo cp -b /usr/bin/qemu-arm-static rootfs_k4b/usr/bin/

// 通过chroot挂载修改
$ sudo chroot rootfs_k4b
# export LC_ALL=C.UTF-8
# echo "nameserver 8.8.8.8" >> etc/resolv.conf
# echo "nameserver 114.114.114.114" >> etc/resolv.conf
// 测试是否有网
# ping www.baidu.com  
# apt-get update
# apt-get upgrade
// 下载需要的东西 或 修改文件
# apt install 		
// chroot下用exit退出
# exit

// 取消挂载(注意：必须取消挂载！！！)
$ ./ch-mount.sh -u rootfs_k4b
```



将修改后的ubuntu重新打包 **(注意：必须取消挂载！！！)**

```shell
rm -v ubuntu-armhf.tar.gz
cd rootfs_k4b/
sudo tar -zcf ../ubuntu-armhf.tar.gz .
cd ..
ls ubuntu-armhf.tar.gz
```

重新编译即可 ./build.sh , 会将 新的 rootfs 打包编译进镜像。

**overlay 方式修改ubuntu**

将需要替换的文件存放到此目录下，编译会拷贝替换 rootfs 对应路径下的文件

```
device/config/rootfs_tar/install_libs/evb1_auto
```



比如，需要将脚本放到 /etc/test.sh，则放到代码路径

```
device/config/rootfs_tar/install_libs/evb1_auto/etc/test.sh
```

编译会拷贝到根目录 /etc/test.sh



## 静态IP配置

``` shell
vim /etc/network/interfaces
添加以下内容

auto eth0
iface eth0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1

根据自己的网关和网段进行配置，否则无法访问网络

配置静态IP 插拔网口ip丢失 解决方法
systemctl stop NetworkManager
systemctl disable NetworkManager

```





## buildroot文件系统修改

详见[buildroot系统定制](./02-buildroot系统定制.md)