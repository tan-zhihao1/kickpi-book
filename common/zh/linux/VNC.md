# VNC

VNC（Virtual Network Computing）是一种基于远程帧缓冲协议（RFB）的图形桌面共享系统，它允许用户通过网络连接到远程计算机，并像操作本地计算机一样操作远程计算机的桌面。



## 符号说明

* `SDK$`：指代源码路径
* `console$`：泛指主板的命令行控制台。[主板命令行控制台](#console_readme)
* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## 主板端配置VNC服务器

**安装**

```
console$ sudo apt update
console$ sudo apt-get install tigervnc-standalone-server tigervnc-common -y
console$ sudo apt-get install tigervnc-tools -y
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



**配置VNC服务**

```
console$ sudo vim /etc/systemd/system/vncserver@.service
[Unit]  
Description=Remote desktop service (VNC)  
After=syslog.target network.target  

[Service]  
Type=forking  
User=user  
PIDFile=/home/user/.vnc/%H:%i.pid  
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



**停止VNC服务**

```
console$ sudo systemctl disable vncserver@1.service  
```



## Windows连接VNC

vnc软件下载

[Download VNC Viewer by RealVNC®](https://www.realvnc.com/en/connect/download/viewer/?lai_vid=EW3mEzvVmIeJO&lai_sr=10-14&lai_sl=l)



**下载Mobaxterm**

Mobaxterm软件地址下载：https://mobaxterm.mobatek.net/



**Mobaxterm操作步骤**

1. 新建会话窗口
2. 选择会话窗口类型为VNC
3. 输入IP地址
4. 输入端口号
5. 启动会话窗口

![image-20250421141430650](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250421141430650.png)

6. 输入密码，点击OK

![image-20250422152029118](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422152029118.png)