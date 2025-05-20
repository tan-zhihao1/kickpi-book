## Linux系统登陆用户密码

Ubuntu系统用户名/密码：kickpi/kickpi

系统默认ROOT密码：root


### chroot 方式修改 ubuntu

搭建构建环境

```shell
sudo apt-get install qemu-user-static
```

修改前先备份保留原本的镜像

```shell
cp device/config/rootfs_tar/ubuntu-armhf.tar.gz device/config/rootfs_tar/ubuntu-armhf.tar.gz-backup
```

解压

```shell
mkdir rootfs_k4b
tar -zxf device/config/rootfs_tar/ubuntu-armhf.tar.gz -C rootfs_k4b
```

chroot 进行挂载，等同于在 root 下进行修改ubuntu镜像

```shell
// 配置以及挂载
$ ./ch-mount.sh -m rootfs_k4b
$ sudo cp -b /etc/resolv.conf rootfs_k4b/etc/resolv.conf
$ sudo cp -b /usr/bin/qemu-aarch64-static rootfs_k5/usr/bin/

// 通过chroot挂载修改
$ sudo chroot rootfs_k5
# export LC_ALL=C.UTF-8
# echo "nameserver 8.8.8.8" >> etc/resolv.conf
# echo "nameserver 114.114.114.114" >> etc/resolv.conf
// 测试是否有网
# ping www.baidu.com  
# apt-get update
# apt-get upgrade
// 下载需要的东西 或 修改文件
# apt install 		
// chroot下用exit退出
# exit

// 取消挂载(注意：必须取消挂载！！！)
$ ./ch-mount.sh -u rootfs_k4b
```



将修改后的ubuntu重新打包 **(注意：必须取消挂载！！！)**

```shell
rm -v device/config/rootfs_tar/ubuntu-armhf.tar.gz
cd rootfs_k4b/
sudo tar -zcf ../device/config/rootfs_tar/ubuntu-armhf.tar.gz .
cd ..
ls device/config/rootfs_tar/ubuntu-armhf.tar.gz
```

重新编译即可 ./build.sh , 会将 新的 rootfs 打包编译进镜像。


## WIFI

### 设置连接wifi

``` shell
touch /etc/wpa_supplicant.conf
vi /etc/wpa_supplicant.conf
ctrl_interface=/etc/wpa_supplicant
ap_scan=1
update_config=1

network={
        ssid="wifi_oranth_2.4"  //修改wifi名
        psk="123456789"		   //修改wifi密码
        key_mgmt=WPA-PSK
}
wpa_supplicant -D nl80211 -i wlan0 -c /etc/wpa_supplicant.conf -B
dhclient wlan0
```

### 设置开机自动连接wifi并获取ip

``` shell
vi /etc/network/interfaces
添加以下内容
auto wlan0
allow-hotplug wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant.conf
```

## 蓝牙

``` shell
hciconfig -a
hciconfig hci0 up
hcitool scan
```

![image-20241216150951975](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216150951975.png)


## 4G
### 拨号

``` shell
pppd call quectel-ppp &
```

![image-20241216145900670](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216145900670.png)

![image-20241216145911055](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216145911055.png)

![image-20241216145926408](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216145926408.png)

## 触控测试

```
evtest
```

![image-20241210113035877](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241210113035877.png)

## 音频

### 耳机/喇叭
``` shell
aplay -Dhw:audiocodec -vv /etc/sample-15s.wav
```
![image-20241216153702549](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216153702549.png)

### 麦克风
``` shell
arecord -Dhw:audiocodec -f S24_LE -r  16000  -vvv  test.wav
```
![image-20241216153822072](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216153822072.png)
``` shell
aplay -Dhw:audiocodec  -vv test.wav
```
![image-20241216153837677](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216153837677.png)



## ssh登录板卡

系统默认支持root登录，可修改/etc/ssh/sshd_config的PermitRootLogin禁止root登录

``` shell
ssh root@<板卡ip>
ssh kickpi@<板卡ip>
```



## USB摄像头测试

### 获取一帧图像

接上USB显示摄像头查看是否有video节点生成

![image-20241216173930265](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241216173930265.png)

``` shell
sudo apt install v4l-utils //安装工具
v4l2-ctl --device=/dev/video0 --stream-mmap=3 --stream-to=test.jpg --stream-count=1
```

v4l2-ctl详细使用说明可以通过v4l2-ctl -h查看

生成的test.jpg可传到window上打开查看

### 显示到lcd上

``` shell
sudo apt install fbi //安装工具
fbi -d /dev/fb0 -T 1 -a test.jpg
```



## 静态IP配置

``` shell
vim /etc/network/interfaces
添加以下内容

auto eth0
iface eth0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1

根据自己的网关和网段进行配置，否则无法访问网络

```





## GPIO控制

### 1查询GPIO管脚号

```shell
cat /sys/kernel/debug/pinctrl/pio/pins | grep PB3
```
### 2管脚控制
```shell
echo 35 > /sys/class/gpio/export
echo out > /sys/class/gpio/gpio35/direction
echo 1 > /sys/class/gpio/gpio35/value
echo 0 > /sys/class/gpio/gpio35/value

direction
	in / out
	echo in > /sys/class/gpio/gpio35/direction
	echo out > /sys/class/gpio/gpio35/direction
value
	0 / 1
	cat /sys/class/gpio/gpio35/value 		// 读取
	echo 1 > /sys/class/gpio/gpio35/value	// 配置高电平
	echo 0 > /sys/class/gpio/gpio35/value  // 配置低电平

```





