# 32-Ubuntu16.04 系统定制

[TOC]





## defconfig 

defconfig 修改及保存

```
确保编译过一次 或 ./build.sh config
cd kernel/linux-4.9/
make ARCH=arm64 menuconfig
cd -
./build.sh saveconfig
```



## 工具链

包路径

```
build/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu.tar.xz
```

编译后工具的路径

```
out/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu
```



## 搭建构建环境

```
sudo apt-get install binfmt-support qemu-user-static
sudo dpkg -i ubuntu-build-service/packages/*
sudo apt-get install -f
```



## ubuntu包路径

ubuntu镜像

```
device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



## chroot 修改ubuntu

修改前先备份保留原本的镜像

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
./ch-mount.sh -m rootfs_k5
sudo cp -b /etc/resolv.conf rootfs_k5/etc/resolv.conf
sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

// 通过chroot挂载修改
sudo chroot rootfs_k5
export LC_ALL=C.UTF-8
# 可以做ubuntu系统下操作

// chroot 下用exit退出
# exit

// 取消挂载
 ./ch-mount.sh -u rootfs_k5/
```



更新apt进行安装

```
ping www.baidu.com  // 测试是否有网
apt-get -y update
apt-get -f -y upgrade
```



将修改后的ubuntu重新打包 **(注意：必须取消挂载！！！)**

```
rm -v device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
cd rootfs_k5/
sudo tar -zcf ../device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz .
cd ..
ls device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



重新编译即可



报错问题

![image-20241022183551403](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241022183551403.png)

修改ubuntu会影响镜像大小，mkfs.ubifs 会报错

需要修改 build/mkcmd.sh

```diff
vim build/mkcmd.sh
- fakeroot mkfs.ubifs -m 4096 -e 258048 -c 1280 -F -x zlib -r ${ROOTFS} -o ${LICHEE_PLAT_OUT}/rootfs.ubifs
+ fakeroot mkfs.ubifs -m 4096 -e 258048 -c 2560 -F -x zlib -r ${ROOTFS} -o ${LICHEE_PLAT_OUT}/rootfs.ubifs
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

编译会拷贝到根目录 /etc/test.sh



## xface 桌面安装（需要联网）

```
$ DEBIAN_FRONTEND=noninteractive apt install -y xubuntu-core onboard rsyslog sudo dialog apt-utils ntp evtest udev --allow-unauthenticated
$ mv /var/lib/dpkg/info/ /var/lib/dpkg/info_old/
$ mkdir /var/lib/dpkg/info/
$ apt-get update
$ DEBIAN_FRONTEND=noninteractive apt install -y xubuntu-core onboard rsyslog sudo dialog apt-utils ntp evtest udev --allow-unauthenticated
$ mv /var/lib/dpkg/info_old/* /var/lib/dpkg/info/
```





