# 01-Linux_Common_System_Customization


## Ubuntu Filesystem Modification  

**Modify Ubuntu via chroot**  

**Set up the build environment**:  
```shell  
sudo apt-get install qemu-user-static  
```

**Backup the original image**:  
```shell  
cp device/config/rootfs_tar/ubuntu-armhf.tar.gz device/config/rootfs_tar/ubuntu-armhf.tar.gz-backup  
```

**Extract the filesystem**:  
```shell  
mkdir rootfs_k4b  
tar -zxf device/config/rootfs_tar/ubuntu-armhf.tar.gz -C rootfs_k4b  
```

**Mount and modify via chroot**:  
```shell  
# Mount the filesystem  
./ch-mount.sh -m rootfs_k4b  

# Copy required files  
sudo cp -b /etc/resolv.conf rootfs_k4b/etc/resolv.conf  
sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k4b/usr/bin/  

# Enter chroot environment  
sudo chroot rootfs_k4b  
export LC_ALL=C.UTF-8  

# Configure network and update  
echo "nameserver 8.8.8.8" >> etc/resolv.conf  
echo "nameserver 114.114.114.114" >> etc/resolv.conf  
ping www.baidu.com  
apt-get update  
apt-get upgrade  

# Install packages or modify files  
apt install <package>  

# Exit chroot  
exit  

# Unmount (Mandatory!)  
./ch-mount.sh -u rootfs_k4b  
```

**Repack the modified filesystem**:  
```shell  
rm -v device/config/rootfs_tar/ubuntu-armhf.tar.gz  
cd rootfs_k4b/  
sudo tar -zcf ../device/config/rootfs_tar/ubuntu-armhf.tar.gz .  
cd ..  
ls device/config/rootfs_tar/ubuntu-armhf.tar.gz  
```

Recompile the system with `./build.sh` to integrate the new rootfs.  

## Buildroot Filesystem Modification  
For details, see [Buildroot System Customization](./02-Buildroot_System_Customization.md).  

