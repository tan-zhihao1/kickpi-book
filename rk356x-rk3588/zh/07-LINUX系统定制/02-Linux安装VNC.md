# 02-Linux安装VNC

VNC（Virtual Network Computing）是一种基于远程帧缓冲协议（RFB）的图形桌面共享系统，它允许用户通过网络连接到远程计算机，并像操作本地计算机一样操作远程计算机的桌面。



> SDK$ - 下文指代源码路径
>
> console$ - 下文泛指主板命令行控制台
>
> ADB$ - Android Debug Bridge命令行工具，下文泛指可运行ADB的环境



## 主板端配置VNC服务器

安装VNC服务

```
console$ sudo apt update
console$ sudo apt-get install 
console$ sudo apt-get install -y tigervnc-standalone-server tigervnc-common tigervnc-tools
```

### 设置 VNC 密码

执行 password 命令设置 VNC 的密码。这不是用户帐户的密码，只是用于 VNC 客户端连接到 VNC 服务器的密码。

```console
console$ vncpasswd
```

记住密码，以便以后在连接客户端时使用。



### 配置桌面启动

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



### 设置 systemd 服务以启动和停止 VNC

要创建 systemd 服务以启动 VNC 服务器，请创建文件`/etc/systemd/system/vncserver@.service`

使用 sudo 或 root，因为它位于只读区域。

```console
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=simple
User=kickpi
PAMName=login
PIDFile=/home/%u/.vnc/%H%i.pid
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :%i > /dev/null 2>&1 || : '
ExecStart=/usr/bin/vncserver :%i -localhost no -geometry 1440x900 -alwaysshared -fg
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

> User=kickpi 对应修改为安装VNC的用户

以下命令适用于使用 .`systemd`

要启动 VNC 服务：

```console
sudo systemctl start vncserver@1.service
```

要停止 VNC 服务：

```console
sudo systemctl stop vncserver@1.service
```

要重新启动 VNC 服务：

```console
sudo systemctl restart vncserver@1.service
```





## Windows连接VNC

**下载Mobaxterm**

Mobaxterm软件地址下载：https://mobaxterm.mobatek.net/



**Mobaxterm操作步骤**

1. 新建会话窗口
2. 选择会话窗口类型为VNC
3. 输入IP地址
4. 输入端口号 (5901)
5. 启动会话窗口

![image-20250428102638564](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428102638564.png)

6. 输入vncpasswd你设置的密码，点击OK

![image-20250422152029118](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422152029118.png)
