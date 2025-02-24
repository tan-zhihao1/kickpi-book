# 20- Linux system customization



## 1. Boot self-starting program

### Boot self-starting script

```
$ /etc/init.d/kickpi.sh
```

Example:

```diff
$ vim /etc/init.d/kickpi.sh
+ source /usr/bin/test_boot.sh
```

Give the script permission to run

```
$ chmod +x /etc/init.d/kickpi.sh
```

> Add the boot startup command to the kickpi.sh script to realize boot self-startup.



## 2. Language Configuration

After the command is executed, the restart takes effect

### Ubuntu 2004

* Set English language

```
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
```

* Set Chinese language

```
$ locale-gen zh_CN.UTF-8
$ sed -i 's/^# *\(zh_CN.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=zh_CN.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
```



## 3. Time zone configuration

After the command is executed, the restart takes effect

* Set time zone Asia/Shanghai

```
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     
```

* Set time zone United States/New York

```
$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```



## 4. Configure the screen display orientation

* View current screen information

```shell
$ xrandr

Screen 0: minimum 320 x 200, current 1920 x 1080, maximum 16384 x 16384
HDMI-1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
   1920x1080     60.00*+  60.00    50.00    59.94    30.00    24.00    29.97    23.98  
   1920x1080i    60.00    50.00    50.00    59.94  
   1280x1024     60.02  
   1440x900      59.90  
   1360x768      60.02  
   1280x720      60.00    50.00    50.00    59.94  
   1024x768      60.00  
   800x600       60.32  
   720x576       50.00    50.00  
   720x576i      50.00  
   720x480       60.00    60.00    59.94    59.94  
   720x480i      60.00    59.94  
   640x480       60.00    59.94    59.94  
```

> According to the printing information, the current system is a single-screen display, and the display device name is HDMI-1.



* Rotate the screen display direction

Set the rotation direction of the specified display device

```
$ xrandr --output (dev) --rotate [normal|left|inverted|right]	
```

> normal: The normal display of the device will be displayed.
>
> left: Rotate the display device 90 degrees counterclockwise.
>
> inverted: Rotates the display device 90 degrees clockwise.
>
> right: Rotate the display device 180 degrees.

Example:

Configure the HDMI-1 display to rotate 90 degrees counterclockwise.

```
$ xrandr --output HDMI-1 --rotate left
```



- Touch calibration after rotating the screen

installation tool

```shell
$ apt update
$ apt install xinput
$ apt install xinput-calibrator
```

Check the device and ID.

```
$ xinput_calibrator --list
	Device "goodix-ts" id=10
```

> According to the printing information, the current touch device is "goodix-ts" and the id is 10.

Configure the calibration matrix of the input device

```
$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
```

> Example:
>
> Set the calibration matrix of the goodix-ts device
>
> $xinput set-prop 10 --type = float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 1.0

Configure input device coordinate transformation matrix

```
// normal
$ xinput set-prop $id 'Coordinate Transformation Matrix' 1 0 0 0 1 0 0 0 1 
// left
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1
// right
$ xinput set-prop $id 'Coordinate Transformation Matrix' 0 1 0 -1 0 1 0 0 1
// inverted
$ xinput set-prop $id 'Coordinate Transformation Matrix' -1 0 1 0 -1 1 0 0 1
```
> Use the corresponding command according to the rotation situation
>
> Example:
>
> Set the coordinate conversion matrix of the goodix-ts device to left
>
> xinput set-prop 10 'Coordinated Transformation Matrix' 0 -1 1 1 0 0 0 0 1

Calibration touch

```
$ xinput_calibrator -v --device $id
```

> Example:
>
Calibration of goodix-ts equipment
>
> $xinput_calibrator -v --device 10



## 5. Network configuration

### Static Internet Protocol Address Configuration

Example: ens33 network interface card configuring static Internet Protocol Address to 192.168.1.50

```
$ vim /etc/netplan/01-network-manager-all.yaml

network:
	version: 2
    ethernets:
        ens33:
            dhcp4: no
            addresses: [192.168.1.50/24]
            optional: true
            gateway4: 192.168.1.1
```

### Common configuration

**Display all network interface information **

```
$ ifconfig -a
enP3p49s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 82:76:b8:2f:96:6d  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 168  base 0xd000  

enP4p65s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 6a:cb:0c:a5:15:e0  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 179  base 0x1000  
...
```

>Network interface names may vary depending on the actual situation



**Display specific network interface information **

```
$ ifconfig <dev>
```

>To display information for a specific network interface (e.g. eth0), type'ifconfig eth0'



**Set IP address **

```
$ ifconfig eth0 <ip> netmask <nmask>
```

> To set the IP address to 192.168.1.101 and the subnet mask to 255.255.255.0 for the eth0 interface, use the command `ifconfig eth0 192.168.1.101 netmask 255.255.255.0`



**Enable network interface **

```
$ ifconfig <dev> up
```

> To enable the'eth0 'interface, you can use the'ifconfig eth0 up' command



**Disable network interface **

```
$ ifconfig <dev> down
```

>To disable the'eth0 'interface, use the'ifconfig eth0 down' command



**Set MAC Address **

```
$ ifconfig <dev> down
$ ifconfig <dev> hw ether <新的MAC地址>
$ ifconfig <dev> up
```

> Different operating systems may have some differences, and some systems may not allow MAC address setting directly through'ifconfig ', or require additional steps



##6. NFS configuration

environment configuration

```
sudo apt install nfs-kernel-server
sudo apt install rpcbind
sudo apt install nfs-common
```

**Server side **

1. Configure shared files

```diff
$ mkdir /home/kickpi/nfs_share
$ chmod 777 /home/kickpi/nfs_share
$ vi /etc/exports
+ /home/kickpi/nfs_share *(rw,sync,no_subtree_check,insecure)
```

2. startup service

```
sudo service nfs-kernel-server restart
```

3. View the current server shared files to prove the shared results

```
$  showmount -e localhost
Export list for localhost:
/home/kickpi/nfs_share *
```

Client side

1. View server shared files

```
showmount -e 192.168.19.173
Export list for 192.168.19.173:
/home/kickpi/nfs_share *
```

2. Mount the folder

```
$ mkdir nfs_tmp
$ sudo mount -t nfs 192.168.19.173:/home/kickpi/nfs_share nfs_tmp/
```

3. Mount successfully

```
$ ls nfs_tmp/
1.txt
```

   

## 7. Mirror source settings

Check the software version and look for mirror source replacements based on the version

```
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.6 LTS
Release:        20.04
Codename:       focal
```



Replace and update the new image source

```
$ sudo vi /etc/apt/sources.list
$ sudo apt update
```

Attention: Use the mirror source of the arm



## 8.Chrome Hardware Acceleration Testing

Test platform: RK3568 Debian11 Chrome

### test method:

1. Copy the 1080P and 4K videos to the board and decode them locally through the Chrome browser.

2. Check GPU occupancy:

```
cat /sys/devices/platform/*gpu/utilisation   //Display results in%
```

3. Open the frame decoding log and print:

```
export mpi_debug=1
export mpp_debug=1
export h264d_debug=1
export mpp_syslog_perror=1
echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

### Test Results：

1080P_30 frames per second: GPU occupancy around 42% -48%

Decoding time test result log segment per frame:

```
[  639.172225] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 4195 us hw 4095 us
[  639.180185] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 12116 us hw 7960 us
[  639.190246] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 19541 us hw 10027 us
[  639.199403] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 22900 us hw 9153 us
[  639.203403] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 20805 us hw 4053 us
[  639.213146] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 21496 us hw 9638 us
[  639.222129] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 21700 us hw 9056 us
[  639.228897] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 24810 us hw 6819 us
[  639.403439] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 6511 us hw 6482 us
[  639.411955] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 11883 us hw 8450 us
[  639.420139] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 17932 us hw 8009 us
[  639.429258] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 24691 us hw 9296 us
[  639.503067] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 5224 us hw 5164 us
[  639.509281] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 8586 us hw 6174 us
[  639.518009] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 14440 us hw 8704 us
[  639.529122] rk_vcodec: fdf80200.rkvdec:0 session 2268:56 time: 22595 us hw 11125 us
```

4K_30 frames per second: GPU occupancy is around 60% -68%

Decoding time test result log segment per frame:

```
[  938.649847] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 93967 us hw 27794 us
[  938.683932] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 93243 us hw 34114 us
[  938.717400] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 93712 us hw 33463 us
[  938.751306] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 100528 us hw 33952 us
[  938.780302] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 95406 us hw 28932 us
[  938.813718] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 94942 us hw 33414 us
[  938.838935] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 86479 us hw 25243 us
[  938.872539] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 91289 us hw 33486 us
[  938.906450] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 91721 us hw 33970 us
[  938.942726] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 102481 us hw 36284 us
[  938.960331] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 86302 us hw 17609 us
[  938.994526] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 86494 us hw 34091 us
[  939.025353] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 80775 us hw 30825 us
[  939.058137] rk_vcodec: fdf80200.rkvdec:0 session 2799:80 time: 96464 us hw 32684 us

```



## 9. SSH configuration

* user：kickpi

* password：kickpi

  Root password change: 

```
$ sudo passwd root
kickpi
kickpi
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

```
$ cat /etc/ssh/ssh_config | grep PermitRootLogin
PermitRootLogin yes
$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin yes
```



## 10. Touch screen, image interface file manager can't open file

1. Open Desktop Home and enter File Manager

2. The second option in the upper right corner edit > > Perferences...

3. Perferences interface, Navigation on the third page Select Single click to activate items



## 11. UBUNTU installs packages from the official website

Installing gcc as an example

`wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb`
`sudo dpkg -i *.deb`
The website is https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html can directly search for the desired dependent package name

![f78e90f7748d198d11dbbd163bb33a9](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f78e90f7748d198d11dbbd163bb33a9.png)



## 12 Backup SD card system

> Note: The rootfs replaced in this way may have a mount-mounted UUID problem. You can blkid to check the correct UUID and then modify/etc/fstab.

Connect the U disk on the board, the size is at least 16GB or more, and the packaged mirror image will be relatively large.

Run our packaging script on the board

```
sudo ./ff_export_rootfs /mnt/usb -t ext4
```

>The generated package name format is as follows: rootfs.img
>
> /mnt/usb directory mounted for USB flash drive

Just burn rootfs separately for the board that needs to replace this system.



## 13 Ubuntu Desktop into root user

The default login user of the latest desktop version is: kickpi.

The desktop version of Terminal may have permission issues using some commands such as: ifconfig

You need root privileges. You can add sudo in front of the command or enter the root user.

Switch the root user as follows:

1. Set the root password

```
$ sudo passwd root
```

Prompt for the current user password, such as: kickpi
Prompt New Password: You can enter the root password you want to set (input will not be displayed)

Retype new password: confirm again

Prompt passwd: password updated successfully indicates success

2 Switch to the root user

```
$ su
```

 Enter the password you just set to let the current Terminal enter the root user



