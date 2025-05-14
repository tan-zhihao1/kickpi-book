# 01-Linux_Common_System_Customization

## Linux System Login Credentials

**Ubuntu Username/Password**: `kickpi/kickpi`  
**ROOT Password**: `root`  

---

## Change Default Login User

**For Serial Port Login**: Modify `/lib/systemd/system/serial-getty@.service.d/override.conf`  
![image-20250305115657169](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305115657169.png)  

---

## Enable ROOT Login for Desktop Environment

1. Modify `/etc/lightdm/lightdm.conf.d/22-autologin.conf`:  
```shell
[Seat:*]
autologin-user=root  # Change to root
autologin-user-timeout=0
user-session=xfce
```

2. Modify `/etc/lightdm/users.conf`:  
```shell
[UserList]
minimum-uid=0  # Allow UID=0 (root) to log in
hidden-users=nobody nobody4 noaccess
hidden-shells=/bin/false /usr/sbin/nologin /sbin/nologin
```

---

## Backup eMMC System to New SD Card (System Backup)

**Steps**:  
1. Insert a high-speed SD card (Class 10 recommended).  
2. Format the SD card:  
   ```shell
   mkfs.ext4 /dev/mmcblk1
   ```
3. Clone the system:  
   ```shell
   dd if=/dev/mmcblk0 of=/dev/mmcblk1 bs=4M
   ```
   ![image-20250304192922929](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250304192922929.png)  

**Notes**:  
- To disable auto-burn service:  
  ```shell
  sudo systemctl disable kickpi.service
  ```
- Fix UUID conflicts by modifying `/etc/fstab` or deleting the file before cloning.  

---

## Backup SD Card System

1. Mount the USB drive:  
   ```shell
   sudo mkdir -p /mnt/usb
   sudo mount /dev/sda1 /mnt/usb
   ```
2. Run the packaging script:  
   ```shell
   sudo ./ff_export_rootfs /mnt/usb/ -t ext4
   ```
   Output format: `Ubuntu22.04.5LTS_ztl_ext4_202411131114.img`  

3. Mount the image for modification:  
   ```shell
   sudo mkdir test_rootfs/
   sudo mount ./Ubuntu22.04.5LTS_ztl_ext4_202411131114.img ./test_rootfs/
   ```

---

## Set Auto-Start Commands

- Add commands to `/etc/rc.local`.  
- Or create service files in `/etc/systemd/system/`.  

---

## Configure WiFi Hotspot (AP Mode)

1. Check AP mode support:  
   ```shell
   iw list | grep AP
   ```
2. Install dependencies:  
   ```shell
   sudo apt-get install util-linux hostapd dnsmasq iptables iproute2 haveged
   ```
3. Create a virtual interface:  
   ```shell
   sudo iw dev wlan0 interface add wlo2 type __ap
   sudo ip link set dev wlo2 address 22:33:44:55:66:00
   ```

4. Use `create_ap` tool:  
   ```shell
   git clone https://github.com/oblique/create_ap
   cd create_ap
   sudo make install
   sudo create_ap -c 11 wlo2 wlan0 MyHotspot 88888888
   ```

---

## Connect to WiFi and Obtain IP

1. Configure `/etc/wpa_supplicant.conf`:  
   ```shell
   network={
       ssid="YourWiFiSSID"
       psk="YourPassword"
       key_mgmt=WPA-PSK
   }
   ```
2. Connect:  
   ```shell
   wpa_supplicant -D nl80211 -i wlan0 -c /etc/wpa_supplicant.conf -B
   dhclient wlan0
   ```

---

## VNC Remote Desktop Login

1. Start VNC server:  
   ```shell
   vncserver
   ```
2. Modify `~/.vnc/xstartup`:  
   ```shell
   unset SESSION_MANAGER
   unset DBUS_SESSION_BUS_ADDRESS
   startxfce4 &
   ```
3. Restart VNC:  
   ```shell
   vncserver -geometry 1920x1080
   ```
4. Connect using RealVNC Viewer:  
   ![image-20250317142654795](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250317142654795.png)  

---

## Install Packages from Ubuntu Repository

Example for installing GCC:  
```shell
wget http://ports.ubuntu.com/pool/main/g/gcc-9/gcc-9_9.3.0-10ubuntu2_arm64.deb
sudo dpkg -i *.deb
```
Search for packages at: [Ubuntu Packages](https://ubuntu.pkgs.org/)  

---

## Troubleshooting

**Issue**: Desktop sound settings crash.  
**Fix**:  
```shell
sudo ln -s pavucontrol pavucontrol-qt
```
![05f3817bf038d6f1f138a290c0496e68](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/05f3817bf038d6f1f138a290c0496e68.jpg)