# 01-Linux_Common_System_Customization

## Linux System Login Credentials

**Ubuntu Username/Password**: `kickpi/kickpi`

---

## GPIO Control

### Sysfs GPIO Control

To configure expansion pins as inputs when the default `gpio-led` driver is insufficient:

**Step 1**: Comment the GPIO node in the device tree.  
Example for PH8:  
```diff
vim device/config/chips/a133/configs/c3/kickpi-k5.dts
+ /*
            PH8 {
                label = "PH8";
                gpios = <&pio PH 8 1 0 1 0>;
                linux,default_trigger = "default-on";
                default-state = "on";
            };
+ */
```

**Step 2**: Recompile and flash the firmware.  

**Step 3**: Verify GPIO availability:  
```bash
cat /sys/kernel/debug/pinctrl/pio/pinmux-pins | grep PH8
# Output: pin 232 (PH8): (MUX UNCLAIMED) (GPIO UNCLAIMED)
```

**Step 4**: Register and control GPIO via sysfs:  
```bash
echo 232 > /sys/class/gpio/export
ls /sys/class/gpio/gpio232

# Control commands:
echo out > /sys/class/gpio/gpio232/direction  # Set as output
echo 1 > /sys/class/gpio/gpio232/value        # Set high
echo 0 > /sys/class/gpio/gpio232/value        # Set low
```

---

### Sunxi GPIO Control

```bash
cd /sys/kernel/debug/sunxi_pinctrl

# View pin configuration
echo PH8 > sunxi_pin
cat sunxi_pin_configure

# Configure as GPIO
echo 'PH8 0' > function

# Modify pull-up/down
echo 'PH8 1' > pull
cat pull

# For PL/R_PIO pins:
echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
```

---

## Ubuntu 1604 Customization

### RootFS Modification via `chroot`

**Prepare Environment**:  
```bash
sudo apt-get install binfmt-support qemu-user-static
sudo dpkg -i ubuntu-build-service/packages/*
```

**Backup & Extract RootFS**:  
```bash
cp device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts_backup.tar.gz
mkdir rootfs_k5
sudo tar -zxf device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz -C rootfs_k5
```

**Modify RootFS**:  
```bash
./ch-mount.sh -m rootfs_k5
sudo chroot rootfs_k5
# Perform modifications (e.g., apt-get update)
exit
./ch-mount.sh -u rootfs_k5
```

**Repackage**:  
```bash
rm device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz
cd rootfs_k5
sudo tar -zcf ../device/config/rootfs_tar/rootfs_ubuntu_kickpi_k5_1604lts.tar.gz .
```

**Troubleshooting**:  
Modify `build/mkcmd.sh` if encountering `mkfs.ubifs` errors:  
```diff
- fakeroot mkfs.ubifs -c 1280 ...
+ fakeroot mkfs.ubifs -c 2560 ...
```

---

### Overlay Modification

Place files under `overlay/` to overwrite rootfs during compilation:  
```bash
overlay/etc/my_script.sh  # Will replace /etc/my_script.sh
```

---

### Export/Repack RootFS

**Export**:  
```bash
ff_export_rootfs /media/kickpi/USB_Drive/
# Output: rootfs.img
```

**Repack**:  
```bash
cp rootfs.img out/a133/c3/bsp/rootfs.ext4
./build.sh pack
```

---

## Partition Modification

**Default Partition Layout**:  
```
/dev/mmcblk0p4: 6G (rootfs)
/dev/mmcblk0p6: 1.1G (UDISK)
```

**Modify Partition Table**:  
```diff
--- a/device/config/chips/a133/configs/default/sys_partition.fex
+++ b/device/config/chips/a133/configs/default/sys_partition.fex
@@ -53,7 +53,7 @@ size = 16384
 
 [partition]
     name         = rootfs
-    size         = 14931722
+    size         = 12582912
```

**Mount UDISK**:  
```bash
mkfs.ext4 /dev/mmcblk0p6
mkdir /data
mount /dev/mmcblk0p6 /data
```

---

## NFS Configuration

**Server**:  
```bash
sudo apt install nfs-kernel-server
echo "/home/user/nfs_share *(rw,sync,no_subtree_check,insecure)" >> /etc/exports
sudo service nfs-kernel-server restart
```

**Client**:  
```bash
sudo mount -t nfs 192.168.1.100:/home/user/nfs_share /mnt/nfs
```

---

## SSH Configuration

**Enable Root Login**:  
```bash
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
systemctl restart ssh
```

---

## WiFi AP Setup

**Install Tools**:  
```bash
apt-get install hostapd dnsmasq
```

**Configure HostAPD**:  
```ini
# /etc/hostapd/hostapd.conf
interface=wlan0
ssid=MyHotspot
wpa_passphrase=password
channel=6
driver=nl80211
```

**Start Services**:  
```bash
systemctl start hostapd dnsmasq
```

---

## VNC Setup

**Install & Configure**:  
```bash
apt-get install tightvncserver
vncserver -geometry 1920x1080
```

**Modify `xstartup`**:  
```bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
startxfce4 &
```

---

## Boot Mode Control

**CLI-Only Boot**:  
```bash
systemctl set-default multi-user.target
reboot
```

**Enable GUI**:  
```bash
systemctl set-default graphical.target
reboot
```

---

## USB-to-UART Support

**Add Device ID**:  
```diff
--- a/kernel/linux-4.9/drivers/usb/serial/option.c
+++ b/kernel/linux-4.9/drivers/usb/serial/option.c
@@ -1980,6 +1980,7 @@ static const struct usb_device_id option_ids[] = {
          { USB_DEVICE(0x2C7C, 0x0306) }, /* Quectel EG06/EP06/EM06 */
          { USB_DEVICE(0x2C7C, 0x0296) }, /* Quectel BG96 */
 #endif
+        { USB_DEVICE(0x1a86, 0x7523) }, /* HL-340 USB-Serial */
```

---

## Miscellaneous

**Fix Firefox**:  
```bash
sudo apt install firefox
```

**Unsupported xrandr Rotation**:  
A133 platform currently does not support display rotation via `xrandr`.
```