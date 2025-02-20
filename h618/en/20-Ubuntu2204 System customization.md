# 20-Ubuntu2204 system



### Linux system login user password

Ubuntu system username/password: kickpi/kickpi

The system does not have a ROOT password by default, su is unsuccessful, and the ROOT password needs to be configured.

```
sudo passwd root
```



## Linux change default login user

/lib/systemd/system/serial-getty@.service.d/override.conf

```
Execstart=-/sbin/agetty -o '-p -f --\\u'--keep-baud --autologin (Automatic login username) 115200,57680,38408,9680 -$TERM
```




## Make an SD startup card

Ubuntu 22.04 system, only image files are provided, please refer to the ARMBIAN SDK for compilation.







## Backup SD card system

#### Allwinner-tina

Connect the U disk on the computer, the size is at least 16GB or more, and the packaged image will be relatively large.

Run our packaging script on the board

```
sudo ./out_rootfs.sh /mnt/usb/ -t ext4
```

> The generated package name format is as follows：Ubuntu22.04.5LTS_ztl_ext4_202411131114.img

Wait for the packaging to end.

Replace this mirror with the original out/h618-linux/rootfs.img

If you need to make further changes to rootfs.img, you can `sudo mount./rootfs.img /test_rootfs/` mount the contents of rootfs.img



Modify the image packaging partition

```
diff --git a/device/config/chips/h618/configs/p2/linux/sys_partition.fex b/device/config/chips/h618/configs/p2/linux/sys_partition.fex
index a5fa88c88..67fb67b5b 100755
--- a/device/config/chips/h618/configs/p2/linux/sys_partition.fex
+++ b/device/config/chips/h618/configs/p2/linux/sys_partition.fex
@@ -53,7 +53,7 @@ size = 16384

 [partition]
     name         = rootfs
-    size         = 8388608
+    size         = 16777216  (Modify it to the size that can accommodate your rootfs.img)
     downloadfile = "rootfs.fex"
     user_type    = 0x8000

```

Then `pack` it into firmware and burn it.

## Customize system functions







## Replace the boot animation picture.

* Replace boot animation picture

```
/usr/share/plymouth/ubuntu-logo.png
/usr/share/plymouth/themes/spinner/watermark.png
/usr/share/plymouth/themes/spinner/bgrt-fallback.png
```

> Replace the custom image with the above three paths
>
> Note that the image format is PNG.



* Update boot animation picture

```
$ sudo update-initramfs -u
$ reboot
```



### Set boot self-start command

Write the required commands into the system /etc/rc.local



### Set the wifi hotspot mode

```
iw list | grep AP
#Check if AP mode is supported
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
# AP/VLAN It can indicate hardware support

#Create dependencies
sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged 

# Create a virtual network interface card
sudo iw dev <wirelessname> interface add <virtualwlanname> type __ap  
# < WirelessName > is the real wireless network interface card name, which can be viewed through ifconfig,  #<virtualwlanname > is the virtual wireless network interface card name
#Such as command 
sudo iw dev wlan0 interface add wlo2 type __ap

#Add physical address for virtual network interface card
sudo ip link set dev <virtualwlanname> address 22:33:44:55:66:00
# Feel free to fill in, if there is a conflict, change it, < virtualwlanname > is the virtual wireless      # network interface card name
#For example, the command:
sudo ip link set dev wlo2 address 22:33:44:55:66:00

#View creation status
sudo iw dev <virtualwlanname> info
sudo iw dev wlo2 info

# The output is similar
   Interface wlo2
	ifindex 5
	wdev 0x5
	addr 04:e2:b9:17:18:72
	type managed
	wiphy 0
	txpower 0.00 dBm
	multicast TXQ:
		qsz-byt	qsz-pkt	flows	drops	marks	overlmt	hashcol	tx-bytestx-packets
		0	0	0	0	0	0	0	0	0
#Note: After restarting the computer, the virtual network interface card created here will be invalid
#Note: After restarting the computer, the virtual network interface card created here will be invalid
#Note: After restarting the computer, the virtual network interface card created here will be invalid


1. Download the installation tool create_ap
git clone https://github.com/oblique/create_ap
cd */create_ap
sudo make install


2. Use create_ap to create hotspots

sudo create_ap -c 11 <virtualwlanname> <wirelessname> <SSID> <password> 

# < WirelessName > is the name of your wireless network interface card, < virtualwlanname > virtual network interface card name, < SSID > < password > is the hotspot wifi name and password created respectively
#For example 
sudo create_ap -c 11 wlo2 wlan0 m3 88888888

3. If the created hotspot gets stuck
Open Hotspot Times with the following error:
#RTNETLINK answers: Device or resource busy

#ERROR: Maybe your WiFi adapter does not fully support virtual interfaces.
     #  Try again with --no-virt.
     
You can stop the hotspot created before, and then restart the hotspot as follows.
sudo create_ap --stop <virtualwlanname>  
#<virtualwlanname> Virtual network interface card name


```



### SSH login

The ubuntu-server version system has SSH installed by default.
Open Windows cmd

```
ssh kickpi@<IP>   //密码 kickpi
```



### SSH root login

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





### UBUNTU installs packages from the official website

Installing gcc as an example

`wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb`
`sudo dpkg -i *.deb`
The website is https://ubuntu.pkgs.org/20.04/ubuntu-main-arm64/gcc-9_9.3.0-10ubuntu2_arm64.deb.html can directly search for the desired dependent package name

![f78e90f7748d198d11dbbd163bb33a9](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/f78e90f7748d198d11dbbd163bb33a9.png)



## PIN control

### Pin calculation

( B ~ J ) A group is counted as 32 bits

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
vim longan/device/config/chips/h618/configs/p2/linux-5.4/board.dts  //android
vim source/kernel/linux-5.4-h618/arch/arm64/boot/dts/sunxi/board.dts //Linux
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

​		Confirm that gpio is not registered

```
root@kickpi:~# cat /sys/kernel/debug/pinctrl/pio/pinmux-pins  | grep PH8
pin 232 (PH8): (MUX UNCLAIMED) (GPIO UNCLAIMED)
```

​		You can see that PH8 is not used, and the corresponding pin value is 232. See pin calculation for pin calculation.



Step four

​		Register and control PH8 through /sys/class/gpio/export

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
# cd /sys/kernel/debug/sunxi_pinctrl

Check the configuration of the pin.
# echo PH8 > sunxi_pin
# cat sunxi_pin_configure

Configure pin to gpio mode
# echo 'PH8 0' > function

Modify the pull-up properties of pin
# echo 'PH8 1' > pull
Check the modification status
# cat pull					
# cat sunxi_pin_configure

Check pin level
# cat data

Attention:
When operating PL and subsequent pins, you need to switch the device of the pin, otherwise the operation fails.
# echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
# cat /sys/kernel/debug/sunxi_pinctrl/dev_name

# echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
# cat /sys/kernel/debug/sunxi_pinctrl/dev_name
```

