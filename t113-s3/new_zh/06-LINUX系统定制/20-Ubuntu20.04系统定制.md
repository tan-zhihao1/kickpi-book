

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





