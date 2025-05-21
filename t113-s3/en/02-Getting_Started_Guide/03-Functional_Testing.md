# 03-Functional_Testing


## LCD  

**Hardware Connection**  
![1732673112060](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1732673112060.png)  

**Display Test**  
```  
fbinit  
echo 8 > /sys/devices/virtual/disp/disp/attr/colorbar  
```  
![image-20241127100853924](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127100853924.png)  

**Touch Test**  
```  
ts_test  
```  
Click the "Draw" button to display touch trajectories:  
![image-20241127101444185](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127101444185.png)  

**Backlight Control**  
```  
/usr/bin/lcd_bright_test 2 255  # Max brightness  
/usr/bin/lcd_bright_test 2 0    # Min brightness  
```  

## Headphone  
```  
tinymix set "Headphone Switch" 1  
aplay -Dhw:audiocodec -vv /etc/sample-15s.wav  
```  
![image-20241127110956315](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127110956315.png)  

## Speaker  
![1730786055916](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1730786055916.png)  

**Play Audio**  
```  
aplay -Dhw:audiocodec -vv /etc/sample-15s.wav  
```  

## Microphone  
**Record & Playback**  
```  
arecord -Dhw:audiocodec -f S24_LE -r 16000 -vvv test.wav  
aplay -Dhw:audiocodec -vv test.wav  
```  

## WiFi  
**Configure WiFi**  
```shell  
# Modify WiFi name and password  
vi /etc/wpa_supplicant.conf  
cat /etc/wpa_supplicant.conf  

ctrl_interface=/var/run/wpa_supplicant  
ap_scan=1  
update_config=1  

network={  
    ssid="wifi_oranth_2.4"  
    psk="123456789"  
    key_mgmt=WPA-PSK  
}  

# Connect to WiFi  
wpa_supplicant -D nl80211 -i wlan0 -c /etc/wpa_supplicant.conf -B  
udhcpc -i wlan0  
```  
![image-20241127182924328](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127182924328.png)  

## Bluetooth  
```  
hciconfig -a  
hciconfig hci0 up  
hcitool scan  
```  
![image-20241213100311376](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241213100311376.png)  

## RTC  
**Power-off Time Retention Test**  
1. Set system time and sync to RTC:  
```  
date -s "2024-11-06 10:30:00" && hwclock -w -f /dev/rtc1  
```  
2. Power off the board, wait, then power it back on.  
3. Verify time consistency:  
```  
date && hwclock -f /dev/rtc1  
```  

## 4G Module  
**Check Connectivity**  
```  
lsusb                     # Verify USB module detection  
ls /dev/ttyUSB*           # Check serial port nodes  
pppd call quectel-ppp &   # Dial-up  
ping www.baidu.com        # Test network  

# Troubleshooting  
ps -ef | grep pppd        # Find process ID  
kill -9 <PID>             # Terminate process  
pppd call quectel-ppp &   # Redial  
```  

## SD Card  
**Speed Test**  
Insert a formatted SD card (auto-mounted):  
```  
time dd if=/dev/zero of=/dev/mmcblk1 bs=1M count=300 oflag=direct  
```  
![image-20241127183750626](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127183750626.png)  

## USB  
**Speed Test**  
Insert a formatted USB drive (auto-mounted):  
```  
time dd if=/dev/zero of=/dev/sda bs=1M count=300 oflag=direct  
```  
![image-20241127183922113](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127183922113.png)  

## CAN Bus  
**Loopback Test**  
1. Connect CANL-to-CANL and CANH-to-CANH:  
![image-20240930140153264](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240930140153264.png)  

2. Initialize CAN interfaces:  
```  
# CAN0  
ip link set can0 down  
ip link set can0 type can bitrate 500000  
ip link set can0 up  

# CAN1  
ip link set can1 down  
ip link set can1 type can bitrate 500000  
ip link set can1 up  
```  

3. Start testing (CAN0 receive, CAN1 transmit):  
```  
candump can0 &  
cansend can1 123#DEADBEEF  
```  
![image-20241127184109190](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241127184109190.png)  
