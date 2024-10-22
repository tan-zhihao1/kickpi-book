# 32-Ubuntu16.04 系统定制

[TOC]



## ubuntu包路径

ubuntu镜像

```
device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



修改前先备份

```
cp device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts_backup.tar.gz
```



解压

```
mkdir rootfs_k5
sudo tar -zxf device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz -C rootfs_k5
```



chroot 进行挂载

```
// 配置以及挂载
sudo mount -t proc /proc rootfs_k5/proc
sudo mount -t sysfs /sys rootfs_k5/sys
sudo mount -o bind /dev rootfs_k5/dev
sudo cp -b /etc/resolv.conf rootfs_k5/etc/resolv.conf
sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

// 通过chroot挂载修改
su chroot rootfs_k5

// chroot 下用exit退出
exit

// 取消挂载
sudo umount rootfs_k5/proc
sudo umount rootfs_k5/sys
sudo umount rootfs_k5/dev
```



更新apt进行安装

```
apt-get -y update
apt-get -f -y upgrade
```

