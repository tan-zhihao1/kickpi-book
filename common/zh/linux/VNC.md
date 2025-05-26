# VNC

VNC（Virtual Network Computing）是一种基于远程帧缓冲协议（RFB）的图形桌面共享系统，它允许用户通过网络连接到远程计算机，并像操作本地计算机一样操作远程计算机的桌面。



## 符号说明

* `console$`：泛指主板的命令行控制台。



## 主板端配置VNC服务器

**安装**

```
console$ sudo apt update
console$ sudo apt-get install tigervnc-standalone-server tigervnc-common tigervnc-tools -y
```



**创建/修改密码**

```
console$ vncpasswd
```

示例：

```
root@linaro-alip:/# vncpasswd  
Password:
Verify:
Would you like to enter a view-only password (y/n)? n
root@linaro-alip:/# 
```



**配置桌面启动**

创建包含以下内容的文件：`$HOME/.vnc/xstartup`

```console
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec startxfce4
```

确保文件具有可执行权限。`xstartup`

```console
console$ chmod +x $HOME/.vnc/xstartup
```





**配置VNC服务**

```
console$ sudo vim /etc/systemd/system/vncserver@.service
[Unit]  
Description=Remote desktop service (VNC)  
After=syslog.target network.target  

[Service]  
Type=forking  
User=kickpi  
PIDFile=/home/kickpi/.vnc/%H:%i.pid  
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1  
ExecStart=/usr/bin/vncserver :%i -geometry 1920x1080 -depth 24  
ExecStop=/usr/bin/vncserver -kill :%i  

[Install]  
WantedBy=multi-user.target
```



**启动VNC服务**

```
console$ sudo systemctl daemon-reload  
console$ sudo systemctl enable vncserver@1.service  
console$ sudo systemctl start vncserver@1.service  
```

> 开启后，开机会自动启动VNC服务



**关闭VNC服务**

```
console$ sudo systemctl disable vncserver@1.service  
```



## Windows连接VNC

**RealVNC软件下载**

[Download VNC Viewer by RealVNC®](https://www.realvnc.com/en/connect/download/viewer/?lai_vid=EW3mEzvVmIeJO&lai_sr=10-14&lai_sl=l)



**RealVNC连接VNC步骤**

1. 输入主板IP和端口，例如 `192.168.77.162:5901`，ENTER键进行连接。
2. 点击 continue 跳过警告。
 ![image-20250514194756724](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250514194756724.png)

3. 输入密码，连接VNC。
