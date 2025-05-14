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
kickpi@kickpi:~$ x11vnc -storepasswd
Enter VNC password: 
Verify password:    
Write password to /home/kickpi/.vnc/passwd?  [y]/n y
Password written to: /home/kickpi/.vnc/passwd
```

> 可知密码存放路径 $(passwd_path) 为 /home/kickpi/.vnc/passwd



**启动VNC服务器**

```
console$ export DISPLAY=:0
console$ x11vnc -display :0 -auth /home/kickpi/.Xauthority -rfbport 5900 -rfbauth $(passwd_path)
```

示例：

```
kickpi@kickpi:~$ export DISPLAY=:0
kickpi@kickpi:~$ x11vnc -display :0 -auth /home/kickpi/.Xauthority -rfbport 5900 -rfbauth /home/kickpi/.vnc/passwd
```





## Windows连接VNC

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