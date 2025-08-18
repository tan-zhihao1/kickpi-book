# Armbian常用系统定制



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 镜像源替换

armbian 官方镜像源

```
console$ cat /etc/apt/sources.list.d/armbian.sources 
Types: deb
URIs: https://apt.armbian.com
Suites: noble
Components: main noble-utils noble-desktop
Signed-By: /usr/share/keyrings/armbian-archive-keyring.gpg
```



Ubuntu 官方镜像源

```
console$ cat /etc/apt/sources.list.d/ubuntu.sources 
Types: deb
URIs: http://ports.ubuntu.com/
Suites: noble noble-security noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```



Ubuntu 24.04 中科大镜像源

```
console$ vim /etc/apt/sources.list.d/ubuntu.sources
Types: deb
URIs: https://mirrors.ustc.edu.cn/ubuntu-ports
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: https://mirrors.ustc.edu.cn/ubuntu-ports
Suites: noble-security
Components: main universe restricted multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
```



Debian 12 中科大镜像源

```
console$ vim /etc/apt/sources.list.d/debian.sources
Types: deb
URIs: http://mirrors.ustc.edu.cn/debian
Suites: bookworm bookworm-updates
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Types: deb
URIs: http://mirrors.ustc.edu.cn/debian-security
Suites: bookworm-security
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```



## 开机启动脚本

KICKPI 默认会启动 kickpi.sh 脚本，可将开机启动的脚本（或其他服务）添加到 kickpi.sh 脚本中。

```
console$ cat /etc/init.d/kickpi.sh 
#!/bin/sh

LOG_FILE=/tmp/kickpi.log
depmod
dmesg -n 1
echo " kickpi.sh run finish !" >> $LOG_FILE
```



## MIPI 屏显示

主板中已存在MIPI屏幕的dtbo文件

```
/boot/dtb-6.1.115-vendor-rk35xx/rockchip/overlay/rk3576-kickpi-lcd-mipi-AT101DS40I.dtbo
/boot/dtb-6.1.115-vendor-rk35xx/rockchip/overlay/rk3576-kickpi-lcd-mipi-MX101BA1340.dtbo
/boot/dtb-6.1.115-vendor-rk35xx/rockchip/overlay/rk3576-kickpi-lcd-mipi-MX080B2140.dtbo
/boot/dtb-6.1.115-vendor-rk35xx/rockchip/overlay/rk3576-kickpi-lcd-mipi-F050008M01.dtbo
```

示例：

板子上接的是 8寸屏，型号为 MX080B2140 ，添加对应的 overlays 名称。重启后，就能点亮对应的屏幕。

```
$ vim /boot/armbianEnv.txt 
verbosity=1
bootlogo=true
console=both
overlay_prefix=rk35xx
fdtfile=rockchip/rk3576-kickpi-k7.dtb
rootdev=UUID=389fa6c5-33a8-4653-99ec-f532b9d5a742
rootfstype=ext4
overlays=rk3576-kickpi-lcd-mipi-MX080B2140
usbstoragequirks=0x2537:0x1066:u,0x2537:0x1068:u
```

> overlays=rk3576-kickpi-lcd-mipi-MX080B2140 添加对应的 dtbo 名字



## 4G/5G 模块

如果监测到4G/5G型号，开机会执行 /usr/bin/quectel-CM 进行拨号上网。

需要定制 4G/5G 模块软件，可通过定制移远官方Linux软件。

https://github.com/quectel-open-source/Quectel_QConnectManager_Linux



## **GCC⼯具链**

**安装命令**

```
apt update && apt install -y build-essential manpages-dev
```

> 安装后，可以在主板上 gcc 编译软件



## Chromium 浏览器

> 推荐使用 chromium 浏览器，目前测试播放视频较为流畅。

**安装方式**

```
console$ sudo apt update
console$ sudo apt install chromium
```



## FireFox 浏览器

**安装方式**

```
console$ sudo apt update
console$ sudo apt install chromium
```

> 不推荐使用 firefox 浏览器，目前会存在 profile 问题，导致重启后无法打开浏览器。

**解决方法一**

在linux的终端输入

```
$ firefox -profilemanager
```

> 删除错误的 profile 
>
> 新建profile 来启动 firefox。

**解决方法二**

```
$ rm ~/.mozilla/firefox/ -rfv
```



## 导出镜像修改 armbian 镜像

第一步，导出文件系统镜像到 U盘/SD卡 等存储路径

```
sudo ./ff_export_rootfs /mnt/ -t ext4
```

第二步，确认文件系统镜像大小，如果大于 armbian镜像

```
// 确认对比镜像大小，导出的 ext镜像接近7G，大于原本的armbian镜像
$ ls -lh *.img
-rwxr--r--  1 hcm  hcm  6.9G Jun 12 16:18 Armbian-unofficial_25.08.0-trunk_noble_ext4_202506121342.img
-rw-rw-r--  1 hcm  hcm  6.3G Jun 12 16:22 armbian.img
```

第三步，如果文件系统镜像大于原本的镜像容量，需要调整原本 armbian 镜像容量

调整armbian的镜像大小为8G

```
$ sudo fallocate -l 9G armbian.img
```

确认节点生成

```
$ sudo losetup -P /dev/loop777 ./armbian.img
```

调整分区大小，使用parted工具

```
$ sudo parted /dev/loop777
# 在parted中执行：
# Fix #选择修复
# (parted) print free  # 查看当前分区和可用空间
# (parted) resizepart 1 100% # 选择调整第一个分区
# (parted) print         # 确认分区信息
# (parted) quit          # 退出
```

调整文件系统大小

```
 # 检查文件系统（可选但推荐）
$ sudo e2fsck -f /dev/loop777p1 
# 扩展文件系统到最大
$ sudo resize2fs /dev/loop777p1  
```

第四步，挂载 armbian镜像文件系统

```
$ sudo mount /dev/loop777p1 orgin/  
```

第五步，挂载导出的文件系统

```
$  sudo mount ext4.img copy/
```

第六步，替换文件系统

```
$ sudo cp -rfp copy/* orgin/
```

第七步，卸载

```
sudo umount copy/
sudo umount orgin/
sudo losetup -d /dev/loop777
```

第八步，修改后的镜像已经完成。



以上步骤也可用脚本进行更新，参考：

```
3-SoftwareData\Linux_backup_rootfs_script\armbian_replace_rootfs.sh
```



## RKNN 部署

armbian 系统支持部署 rknn-toolkit2，rknn_model_zoo，参考 rockhip 官方文档如下：

https://github.com/airockchip/rknn-toolkit2/tree/master/doc

https://github.com/airockchip/rknn_model_zoo/tree/main/docs



## ARMBIAN 源码修改 patch 参考

