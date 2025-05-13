# 01-Common_Linux_System_Customization

## Boot Autostart Programs

### Boot Autostart Script

```
$ /etc/init.d/kickpi.sh
```

Example:

```diff
$ vim /etc/init.d/kickpi.sh
+ source /usr/bin/test_boot.sh
```

Give the script execution permissions:

```
$ chmod +x /etc/init.d/kickpi.sh
```

> Add the boot autostart program commands to the `kickpi.sh` script to achieve boot autostart.

## Modify Serial Port Auto-Login User

By default, the serial port auto-logs in as the `root` user. The following example shows how to change it to `kickpi`:

```
$ vim /lib/systemd/system/serial-getty@.service
-ExecStart=-/sbin/agetty --autologin root --keep-baud 115200,38400,9600 %I $TERM
+ExecStart=-/sbin/agetty --autologin kickpi --keep-baud 115200,38400,9600 %I $TERM
```

## Language Configuration

> After executing the commands, restart the system for the changes to take effect.

### Ubuntu 20.04

* Set the language to English

```
$ locale-gen en_US.UTF-8
$ sed -i 's/^# *\(en_US.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=en_US.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=en_US.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=en_US:en" >> /etc/profile.d/zh_CN.sh
```

* Set the language to Chinese

```
$ locale-gen zh_CN.UTF-8
$ sed -i 's/^# *\(zh_CN.UTF-8\)/\1/' /etc/locale.gen
$ echo "LANG=zh_CN.UTF-8" >> /etc/default/locale
$ echo "export LC_ALL=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANG=zh_CN.UTF-8" >> /etc/profile.d/zh_CN.sh
$ echo "export LANGUAGE=zh_CN:zh" >> /etc/profile.d/zh_CN.sh
```

## Time Zone Configuration

> After executing the commands, restart the system for the changes to take effect.

* Set the time zone to Asia/Shanghai

```
$ ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

* Set the time zone to America/New York

```
$ ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```

## Configure Screen Display Orientation

* View current screen information

```
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

> Based on the printed information, the current system has a single-screen display, and the display device name is `HDMI-1`.

* Rotate the screen display orientation

Set the rotation direction of the specified display device:

```
$ xrandr --output (dev) --rotate [normal|left|inverted|right]
```

> `normal`: Display the device normally.
>
> `left`: Rotate the display device counterclockwise by 90 degrees.
>
> `inverted`: Rotate the display device clockwise by 90 degrees.
>
> `right`: Rotate the display device by 180 degrees.

Example:

Configure the `HDMI-1` display to rotate counterclockwise by 90 degrees:

```
$ xrandr --output HDMI-1 --rotate left
```

- Touch calibration after screen rotation

Install tools:

```
$ apt update
$ apt install xinput
$ apt install xinput-calibrator
```

View devices and IDs:

```
$ xinput_calibrator --list
    Device "goodix-ts" id=10
```

> Based on the printed information, the current touch device is "goodix-ts" with an ID of 10.

Configure the calibration matrix for the input device:

```
$ xinput set-prop $id --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0
```

> Example:
>
> Set the calibration matrix for the `goodix-ts` device:
>
> `$ xinput set-prop 10 --type=float "libinput Calibration Matrix" 1.0 0.0 0.0 0.0 1.0 0.0 0.0 0.0 1.0`

Configure the coordinate transformation matrix for the input device:

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

> Use the corresponding command according to the rotation situation.
>
> Example:
>
> Set the coordinate transformation matrix for the `goodix-ts` device to `left`:
>
> `xinput set-prop 10 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1`

Calibrate the touch:

```
$ xinput_calibrator -v --device $id
```

> Example:
>
> Calibrate the `goodix-ts` device:
>
> `$ xinput_calibrator -v --device 10`

### Permanently modify the touch direction

You can refer to the LCD configuration document: [Single Touch Screen Driver Configuration](40-RK3568 LCD configuration.md#TouchDriver)

## Network Configuration

### Static IP Address Configuration

Example: Configure the static IP address of the `eth0` network card to `192.168.1.50`:

```shell
$ vim /etc/netplan/01-network-manager-all.yaml

network:
    version: 2
    ethernets:
        eth0:
            dhcp4: no
            addresses: [192.168.1.50/24]
            optional: true
            gateway4: 192.168.1.1
```

Restart the service:

```shell
$ reboot
```

### Common Configurations

**Display information for all network interfaces**

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

> The network interface names may vary depending on the actual situation.

**Display information for a specific network interface**

```
$ ifconfig <dev>
```

> To display information for a specific network interface (e.g., `eth0`), you can enter `ifconfig eth0`.

**Set the IP address**

```
$ ifconfig eth0 <ip> netmask <nmask>
```

> To set the IP address of the `eth0` interface to `192.168.1.101` and the subnet mask to `255.255.255.0`, you can use the command `ifconfig eth0 192.168.1.101 netmask 255.255.255.0`.

**Enable a network interface**

```
$ ifconfig <dev> up
```

> To enable the `eth0` interface, you can use the `ifconfig eth0 up` command.

**Disable a network interface**

```
$ ifconfig <dev> down
```

> To disable the `eth0` interface, use the `ifconfig eth0 down` command.

**Set the MAC address**

```
$ ifconfig <dev> down
$ ifconfig <dev> hw ether <new MAC address>
$ ifconfig <dev> up
```

> Different operating systems may have some differences. Some systems may not allow you to directly set the MAC address through `ifconfig`, or additional steps may be required.

## Command-Line Configuration of WIFI Hotspot (AP) Mode

**Check if AP mode is supported**

```shell
$ iw list | grep AP
Device supports AP-side u-APSD.
         * AP
         * AP/VLAN
        HE Iftypes: AP
        HE Iftypes: AP
         * wake up on EAP identity request
         * AP/VLAN
         * #{ managed } <= 1, #{ AP, P2P-client, P2P-GO } <= 1, #{ P2P-device } <= 1,
        Driver supports full state transitions for AP/GO clients
        Driver/device bandwidth changes during BSS lifetime (AP/GO mode)
         * AP: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
         * AP/VLAN: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
         * AP: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
         * AP/VLAN: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0

```

> `AP/VLAN` indicates that the hardware supports AP mode.

### Create a Virtual Interface to Create a Hotspot Mode

Install dependent packages:

```shell
$ sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged make
```

Create a virtual network card:

```shell
$ sudo iw dev <wirelessname> interface add <virtualwlanname> type __ap
```

> `<wirelessname>` is the name of the real wireless network card, which can be viewed through `ifconfig`. `<virtualwlanname>` is the name of the virtual wireless network card.

For example:

```shell
$ sudo iw dev wlan0 interface add wlo2 type __ap
```

Add a physical address to the virtual network card:

```shell
$ sudo ip link set dev <virtualwlanname> address 22:33:44:55:66:00
```

> You can fill in any address. If there is a conflict, try another one. `<virtualwlanname>` is the name of the virtual wireless network card.

For example:

```shell
$ sudo ip link set dev wlo2 address 22:33:44:55:66:00
```

View the creation status:

```shell
$ sudo iw dev wlo2 info
   Interface wlo2
    ifindex 5
    wdev 0x5
    addr 22:33:44:55:66:00
    type managed
    wiphy 0
    txpower 0.00 dBm
    multicast TXQ:
        qsz-byt qsz-pkt flows drops marks overlmt hashcol tx-bytes tx-packets
        0       0       0     0     0     0       0       0       0
```

> Note: The virtual network card created here will become invalid after restarting the computer.

Download and install the `create_ap` tool:

```shell
$ git clone https://github.com/oblique/create_ap
$ cd */create_ap
$ sudo make install
```

Use `create_ap` to create a hotspot:

```shell
$ sudo create_ap -c 11 <virtualwlanname> <wirelessname> <SSID> <password>
```

> `<wirelessname>` is the name of your wireless network card, `<virtualwlanname>` is the name of the virtual network card, and `<SSID>` and `<password>` are the name and password of the created hotspot Wi-Fi, respectively.

For example:

```shell
$ sudo create_ap -c 11 wlo2 wlan0 m3 88888888
```

If the created hotspot gets stuck and the following error is reported when starting the hotspot:

```
#RTNETLINK answers: Device or resource busy

#ERROR: Maybe your WiFi adapter does not fully support virtual interfaces.
     #  Try again with --no-virt.
```

You can stop the previously created hotspot as follows and then restart it:

```shell
$ sudo create_ap --stop <virtualwlanname>
```

> `<virtualwlanname>` is the name of the virtual network card.

### Use a Physical Interface to Create a Hotspot Mode

Install dependencies and `create_ap`:

```shell
$ sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged  make
$ git clone https://github.com/oblique/create_ap
$ cd */create_ap
$ sudo make install
```

Modify the firewall:

```shell
$ sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
$ sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
$ iptables --version
```

Create a hotspot named `MyAccessPoint` with a password of `12345678` and share the `eth0` network:

```shell
$ sudo create_ap --no-virt wlan0 eth0 MyAccessPoint 12345678 &
```

## NFS Configuration

Environment configuration:

```
sudo apt install nfs-kernel-server
sudo apt install rpcbind
sudo apt install nfs-common
```

**Server Side**

1. Configure the shared files:

```diff
$ mkdir /home/kickpi/nfs_share
$ chmod 777 /home/kickpi/nfs_share
$ vi /etc/exports
+ /home/kickpi/nfs_share *(rw,sync,no_subtree_check,insecure)
```

2. Start the service:

```
sudo service nfs-kernel-server restart
```

3. View the currently shared files on the server to verify the sharing:

```
$  showmount -e localhost
Export list for localhost:
/home/kickpi/nfs_share *
```

**Client Side**

1. View the shared files on the server:

```
showmount -e 192.168.19.173
Export list for 192.168.19.173:
/home/kickpi/nfs_share *
```

2. Mount the folder:

```
$ mkdir nfs_tmp
$ sudo mount -t nfs 192.168.19.173:/home/kickpi/nfs_share nfs_tmp/
```

3. Verify the successful mount:

```
$ ls nfs_tmp/
1.txt
```

## Mirror Source Settings

Check the software version and find the mirror source to replace according to the version:

```
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.6 LTS
Release:        20.04
Codename:       focal
```

Replace the new mirror source and update:

```
$ sudo vi /etc/apt/sources.list
$ sudo apt update
```

Note: Use the ARM mirror source.

## Chrome Hardware Acceleration Test

Test Platform: RK3568 Debian 11 Chrome

#### Test Method:

1. Copy 1080P and 4K videos to the board and decode them locally through the Chrome browser.
2. Check the GPU usage:

```
cat /sys/devices/platform/*gpu/utilisation   // The display result is in percentage.
```

3. Enable and print the frame decoding log:

```
export mpi_debug=1
export mpp_debug=1
export h264d_debug=1
export mpp_syslog_perror=1
echo 0x100 > /sys/module/rk_vcodec/parameters/mpp_dev_debug
```

#### Test Results:

1080P_30fps: The GPU usage is around 42% - 48%.

Log snippet of the test results for each frame decoding time:

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

4K_30fps: The GPU usage is around 60% - 68%.

Log snippet of the test results for each frame decoding time:

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

## SSH Configuration

```
User: kickpi
Password: kickpi
Change the root password: 
$ sudo passwd root
kickpi
kickpi
```

By default, root connection is not supported. To enable root connection, you need to configure it as follows:

```shell
$ vim /etc/ssh/ssh_config
+ PermitRootLogin yes
$ vim /etc/ssh/sshd_config
+ PermitRootLogin yes
$ sudo /etc/init.d/ssh restart
```

**Precautions**:

- Ensure that the board's IP address is valid.
- Ensure that you can connect to the board's IP address normally.

```
$ cat /etc/ssh/ssh_config | grep PermitRootLogin
PermitRootLogin yes
$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin yes
```

## USB Camera

**Check the camera device nodes**

```
$ ls /dev/video*
/dev/video-dec0  /dev/video-enc0  /dev/video0  /dev/video1  /dev/video2
```

**Open the camera with ffmpeg**

```
$ sudo apt install ffmpeg 
$ ffplay -f v4l2 -i /dev/video* 
```

Example:

```
$ ffplay -f v4l2 -i /dev/video1
```

## The File Manager in the Touch Screen GUI Cannot Open Files

1. Open the desktop `Home` and enter the file manager.
2. Click the second option in the upper right corner: `Edit` >> `Preferences...`
3. On the `Preferences` interface, go to the third page `Navigation` and select `Single click to activate items`.

## Install Software Packages from the Official Ubuntu Website

Take installing `gcc` as an example:

```
wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb
sudo dpkg -i *.deb
```

The website is [https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html](https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html). You can directly search for the name of the required dependency package.

![f78e90f7748d198d11dbbd163bb33a9](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f78e90f7748d198d11dbbd163bb33a9.png)

## Backup the rootfs System

> Note: After replacing the rootfs in this way, there may be issues with the mount UUID. You can use `blkid` to view the correct UUID and then modify `/etc/fstab`.

Connect a USB drive to the board. The USB drive should be at least 16GB in size because the packaged image will be relatively large.

The script is located in the network disk:

```
rk356x_data\3-SoftwareData software materials\backup rootfs
```

Copy the script to the Linux system on the board and run it:

```shell
$ sudo chmod +x ./ff_export_rootfs
$ sudo ./ff_export_rootfs /mnt/usb -t ext4
```

> The generated package name will be in the format like `rootfs.img`.
>
> `/mnt/usb` is the mount directory of the USB drive.

For the board that needs to replace this system, you can burn the rootfs separately. If you need to repackage it into a complete image, refer to [Firmware Unpacking and Packing](../08-Advanced/03-Firmware Unpacking and Packing.md).

## Enter the root User in the Ubuntu Desktop Edition

The default login user for the latest desktop edition system is `kickpi`.

When using some commands in the Terminal of the desktop edition system, you may encounter permission issues, such as `ifconfig`.

In this case, you need root permissions. You can add `sudo` before the command or switch to the root user.

The steps to switch to the root user are as follows:

1. Set the root password:

```
$ sudo passwd root
```

Enter the current user password, e.g., `kickpi`.
Then enter the new root password (the input will not be displayed).
Confirm the password again.
If you see the message `passwd: password updated successfully`, it means the password has been set successfully.

2. Switch to the root user:

```
$ su
```

Enter the password you just set, and the current Terminal will enter the root user mode.

## Enter the Linux Command-Line Interface

### Ubuntu System

Turning off the Ubuntu graphical desktop will make the system enter the Linux command-line interface. The following describes how to turn the Ubuntu graphical desktop on and off.

Temporarily turn off the desktop:

```shell
$ sudo systemctl stop slim.service
```

Temporarily turn on the desktop:

```shell
$ sudo systemctl start slim.service
```

Permanently disable the desktop:

```shell
$ sudo systemctl disable slim.service
```

Permanently enable the desktop:

```shell
$ sudo systemctl enable slim.service
```

If the GUI desktop freezes, you can use the keyboard to enter the command-line interface:

- To enter the command-line interface, press the shortcut keys `CTRL + ALT + F3`.
- To return to the GUI desktop, press the shortcut key `ALT + F2`.

### Debian System

#### Disable the desktop environment and start in pure command-line mode

Modify the default runlevel:

```shell
$ systemctl set-default multi-user.target
```

Check the current system runlevel:

```shell
$ systemctl get-default
```

Restart the system:

```shell
$ reboot
```

#### Enable the desktop environment

Modify the default runlevel:

```shell
$ systemctl set-default graphical.target
```

Restart the system:

```shell
$ reboot
```