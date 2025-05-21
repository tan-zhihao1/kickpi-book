# 01-Linux常用系统定制

## Linux系统登陆用户密码

Ubuntu系统用户名/密码：kickpi/kickpi

系统默认ROOT密码：root


### chroot 方式修改 ubuntu

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
mkdir rootfs_k4b
tar -zxf device/config/rootfs_tar/ubuntu-armhf.tar.gz -C rootfs_k4b
```

chroot 进行挂载，等同于在 root 下进行修改ubuntu镜像

```shell
// 配置以及挂载
$ ./ch-mount.sh -m rootfs_k4b
$ sudo cp -b /etc/resolv.conf rootfs_k4b/etc/resolv.conf
$ sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

// 通过chroot挂载修改
$ sudo chroot rootfs_k5
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
rm -v device/config/rootfs_tar/ubuntu-armhf.tar.gz
cd rootfs_k4b/
sudo tar -zcf ../device/config/rootfs_tar/ubuntu-armhf.tar.gz .
cd ..
ls device/config/rootfs_tar/ubuntu-armhf.tar.gz
```

重新编译即可 ./build.sh , 会将 新的 rootfs 打包编译进镜像。