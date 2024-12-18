# 32-Ubuntu16.04 system customization

[TOC]

## compilation method

​		See 11 - SDK source code compilation



## DTS

```
device/config/chips/a133/configs/c3/board.dts
```



## Kernel Defconfig 

Defconfig and save

```
Make sure it is compiled once, or ./build.sh config
cd kernel/linux-4.9/
make ARCH=arm64 menuconfig
cd -
./build.sh saveconfig
```



## toolchain

packet path

```
build/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu.tar.xz
```

Path to the compiled tool

```
out/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu
```



## PIN control

### Pin calculation

(B~ J) A group is calculated according to 32 bits

If PB2 is 2 groups, foot 2

```
32 x ( 2 - 1) + 2 =  34
```

PH3 is 8 groups, foot 3.

```
32 x ( 8 - 1) + 3 =  227 
```

Other pins can be calculated by analogy.



### Sys gpio control

When there is a need to configure the extension pin as an input, the default software's GPIO-LED does not meet the requirement.

Release the GPIO and control it with/sys/class/gpio



Step one

Note the corresponding GPIO pins first,/sys/class/gpio/export can only import unregistered gpio.

For example, PH8, if you need other pins, just comment under the leds node. The following content is based on PH8 as an example

```diff
vim device/config/chips/a133/configs/c3/board.dts
+ /*
			PH8 {
				label = "PH8";
				gpios = <&pio PH 8 1 0 1 0>;
				linux,default_trigger = "default-on";
				default-state = "on";
			};
+ */
```



Step two

​	Compilation mirroring, reburning

Step three

​	Confirm that gpio is not registered

```
root@kickpi:~# cat /sys/kernel/debug/pinctrl/pio/pinmux-pins  | grep PH8
pin 232 (PH8): (MUX UNCLAIMED) (GPIO UNCLAIMED)
```

​		You can see that PH8 is not used, and the corresponding pin value is 232. See pin calculation for pin calculation.



Step four

​	Register and control PH8 by /sys/class/gpio/export

```
// Register
root@kickpi:~# echo  232 > /sys/class/gpio/export
// Check if it is generated
root@kickpi:~# ls /sys/class/gpio/
export  gpio232  gpiochip0  gpiochip352  unexport
// Node content after registration
root@kickpi:~# ls /sys/class/gpio/gpio232
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

​			Control gpio through the content under the node, commonly used as follows

```
direction
	in / out
	echo in > /sys/class/gpio/gpio232/direction
	echo out > /sys/class/gpio/gpio232/direction
value
	0 / 1
	cat /sys/class/gpio/gpio232/value 		// read
	echo 1 > /sys/class/gpio/gpio232/value	// Configure High
	echo 0 > /sys/class/gpio/gpio232/value  // Configure low
```



### Sunxi gpio control

```
cd /sys/kernel/debug/sunxi_pinctrl

Check the configuration of the pin.
# echo PH8 > sunxi_pin
# cat sunxi_pin_configure

Configure pin to gpio mode
# echo 'PH8 0' > function

Modify the pull-up properties of pin
# echo 'PH8 1' > pull
//check the modification status
# cat pull					
# cat sunxi_pin_configure

Check pin level
# cat data

Attention:
When operating PL and subsequent pins, you need to switch the device of the pin, otherwise the operation fails.
echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
cat /sys/kernel/debug/sunxi_pinctrl/dev_name

echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
cat /sys/kernel/debug/sunxi_pinctrl/dev_name
```



## UBUNTU 1604

Ubuntu file system

```
device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



### Modify Ubuntu by chroot

Setting up the build environment

```
sudo apt-get install binfmt-support qemu-user-static
sudo dpkg -i ubuntu-build-service/packages/*
sudo apt-get install -f
```



Backup before modification and keep the original image

```
cp device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts_backup.tar.gz
```



decompress

```
mkdir rootfs_k5
sudo tar -zxf device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz -C rootfs_k5
```



Chroot to mount, which is equivalent to modifying the ubuntu image under root

```shell
//Configure and mount
$ ./ch-mount.sh -m rootfs_k5
$ sudo cp -b /etc/resolv.conf rootfs_k5/etc/resolv.conf
$ sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

//Modify by chroot mount
$ sudo chroot rootfs_k5
# export LC_ALL=C.UTF-8
# echo "nameserver 8.8.8.8" >> etc/resolv.conf
# echo "nameserver 114.114.114.114" >> etc/resolv.conf
//Test if there is a network
# ping www.baidu.com  
# apt-get update
# apt-get upgrade
Download what you need, or modify the file
# apt install 		
//Exit with exit under chroot
# exit

//unmount
$ ./ch-mount.sh -u rootfs_k5
```



Repack modified Ubuntu  **(Note: must unmount!!!)** 

```
rm -v device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
cd rootfs_k5/
sudo tar -zcf ../device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz .
cd ..
ls device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
```



Just recompile./build.sh, the new root fs will be packaged and compiled into the image.

Error report

![image-20241022183551403](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241022183551403.png)

Modifying ubuntu will affect the image size, and mkfs.ubifs will report an error.

Need to modify build/mkcmd.sh

```diff
vim build/mkcmd.sh
- fakeroot mkfs.ubifs -m 4096 -e 258048 -c 1280 -F -x zlib -r ${ROOTFS} -o ${LICHEE_PLAT_OUT}/rootfs.ubifs
+ fakeroot mkfs.ubifs -m 4096 -e 258048 -c 2560 -F -x zlib -r ${ROOTFS} -o ${LICHEE_PLAT_OUT}/rootfs.ubifs
```



Note: If the chroot modification method does not take effect, you need to check whether there is an overlay file in the file under the overlay path!



### Modify Ubuntu by overlay

Store the files that need to be replaced under the overlay, and the compilation will copy and replace the files in the corresponding path of rootfs.

```
overlay
```



For example, if you need to put the script in /etc/test.sh, put it in the code path

```
overlay/etc/test.sh
```

The compilation will be copied to the root directory /etc/test.sh



## Make a packaged root file system

Objective: To export the modified configuration of the board to the root file system image and repackage it into a burning image

```bash
# ff_export_rootfs The path to the U disk or SD card
如：
# ff_export_rootfs /media/kickpi/EAA1-F4D4/	
	Export rootfs to /media/kickpi/EAA1-F4D4//rootfs.img Success
# ls /media/kickpi/EAA1-F4D4/
	rootfs.img
```



On the already compiled code, repackage the generated rootfs.img

```bash
rm out/a133/c3/bsp/rootfs.ext4
cp (Path to rootfs.img) out/a133/c3/bsp/rootfs.ext4
./build.sh pack
```



## Partition and capacity

At present, the default rootfs partition of the software is 6G. In addition to other necessary partitions, the remaining space is allocated to the UDISK partition `/dev/mmcblk0p6`

```
# fdisk  -l
Device            Start      End  Sectors  Size Type
/dev/mmcblk0p1    73728   139263    65536   32M Microsoft basic data
/dev/mmcblk0p2   139264   172031    32768   16M Microsoft basic data
/dev/mmcblk0p3   172032   303103   131072   64M Microsoft basic data
/dev/mmcblk0p4   303104 12886015 12582912    6G Microsoft basic data
/dev/mmcblk0p5 12886016 12918783    32768   16M Microsoft basic data
/dev/mmcblk0p6 12918784 15155166  2236383  1.1G Microsoft basic data
```



Modify partition table

```
vim device/config/chips/a133/configs/default/sys_partition.fex
```





The UDISK partition is not mounted by default, it can be mounted manually

```
// Default filesystem format, format it not ext4
sudo mkfs.ext4 /dev/mmcblk0p6

// Mount UDISK partition
mkdir /data
mount /dev/mmcblk0p6 /data
```



Fstab modification method, system start-up will mount it by default

```
# vim /etc/fstab
/dev/mmcblk0p6  /data       ext4    defaults        0       0
```



## NFS configuration

**environment configuration**

```
sudo apt install nfs-kernel-server
sudo apt install rpcbind
sudo apt install nfs-common
```



**server side**

1. Configure shared files

```
$ mkdir /home/huangcm/nfs_share
$ chmod 777 /home/huangcm/nfs_share
$ vi /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
# 
/home/huangcm/nfs_share *(rw,sync,no_subtree_check,insecure)
```



2. startup service

```
sudo service nfs-kernel-server restart
```



3. View the current server shared files to prove the shared results

```
$  showmount -e localhost
Export list for localhost:
/home/huangcm/nfs_share *
```



**client side**

1. View server shared files

```
showmount -e 192.168.199.173
Export list for 192.168.199.173:
/home/huangcm/nfs_share *
```



2. mount folder

```
$ mkdir nfs_tmp
$ sudo mount -t nfs 192.168.199.173:/home/huangcm/nfs_share nfs_tmp/
```



3. Mount successful

```
$ ls nfs_tmp/
1.txt
```

   

## SSH configuration

```
User:kickpi
Password: kickpi
(su)root Password： kickpi
```



Root connection is not supported by default, root connection needs to be configured.

```shell
$ vim /etc/ssh/ssh_config
+ PermitRootLogin yes
$ vim /etc/ssh/sshd_config
+ PermitRootLogin yes
$ sudo /etc/init.d/ssh restart
```

Notes:

Make sure the board IP is normal.

Make sure that the IP of the board can be connected normally.



## Other business

### XFCE browser cannot be opened

Firefox browser needs to be installed.

```
sudo apt install firefox
```



### Xrandr cannot rotate

​		A133 currently does not support xrandr rotation

