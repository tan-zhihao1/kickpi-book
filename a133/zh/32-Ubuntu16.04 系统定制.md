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



chroot 进行修改

```
sudo mount -t proc /proc rootfs_k5/proc
sudo mount -t sysfs /sys rootfs_k5/sys
sudo mount -o bind /dev rootfs_k5/dev



sudo umount rootfs_k5/proc
sudo umount rootfs_k5/sys
sudo umount rootfs_k5/dev
```



