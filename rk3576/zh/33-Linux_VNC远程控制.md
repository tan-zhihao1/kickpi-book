# 05-Linux_VNC远程控制

VNC（Virtual Network Computing）是一种基于远程帧缓冲协议（RFB）的图形桌面共享系统，它允许用户通过网络连接到远程计算机，并像操作本地计算机一样操作远程计算机的桌面。



> SDK$ - 下文指代源码路径
>
> console$ - 下文泛指主板命令行控制台
>
> ADB$ - Android Debug Bridge命令行工具，下文泛指可运行ADB的环境



## 主板端配置VNC服务器

**配置环境**

```
console$ sudo apt update
console$ sudo apt install x11vnc
```



**创建密码**

```
console$ x11vnc -storepasswd
```

示例：

```
kickpi@kickpi:~$ x11vnc -storepasswd
Enter VNC password: 
Verify password:    
Write password to /home/kickpi/.vnc/passwd?  [y]/n y
Password written to: /home/kickpi/.vnc/passwd
```

> 可知密码存放路径为 /home/kickpi/.vnc/passwd



**启动VNC服务器**

```
console$ export DISPLAY=:0
console$ x11vnc -auth guess -once -loop -noxdamage -repeat -rfbauth /home/kickpi/.vnc/passwd -rfbport 5900 -shared
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