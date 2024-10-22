# 32-Ubuntu16.04 系统定制

[TOC]



## ubuntu包路径

ubuntu镜像

```
device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



## chroot 修改ubuntu

修改前先备份

```
cp device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts_backup.tar.gz
```



解压

```
mkdir rootfs_k5
sudo tar -zxf device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz -C rootfs_k5
```



chroot 进行挂载，等同于在 root 下进行修改ubuntu镜像

```
// 配置以及挂载
sudo mount -t proc /proc rootfs_k5/proc
sudo mount -t sysfs /sys rootfs_k5/sys
sudo mount -o bind /dev rootfs_k5/dev
sudo cp -b /etc/resolv.conf rootfs_k5/etc/resolv.conf
sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

// 通过chroot挂载修改
su chroot rootfs_k5
# export LC_ALL=C.UTF-8
# 可以做ubuntu系统下操作

// chroot 下用exit退出
# exit

// 取消挂载
sudo umount rootfs_k5/proc
sudo umount rootfs_k5/sys
sudo umount rootfs_k5/dev
```



更新apt进行安装

```
ping www.baidu.com  // 测试是否有网
apt-get -y update
apt-get -f -y upgrade
```



将修改后的ubuntu重新打包 (注意：必须取消挂载！！！)

```
rm -v device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
cd rootfs_k5/
sudo tar -zcf ../device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz .
cd ..
ls device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



## 替换方式修改ubuntu

将需要替换的文件存放到overlay下，编译会拷贝替换 rootfs 对应路径下的文件

```
overlay
```



比如，需要将脚本放到 /etc/test.sh，则放到代码路径

```
overlay/etc/test.sh
```

编译会拷贝到根目录 etc/test.sh





