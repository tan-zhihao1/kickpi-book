# 05-Linux_VNC Remote Control

VNC (Virtual Network Computing) is a graphical desktop sharing system based on the Remote Frame Buffer protocol (RFB). It allows users to connect to a remote computer over the network and operate the desktop of the remote computer as if it were a local one.

## Symbol Explanation

* `SDK$`: Represents the source code path.
* `console$`: Generally refers to the command-line console of the motherboard. [Motherboard command-line console](#console_readme)
* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

## Configure the VNC Server on the Motherboard

### Configuration Environment

```
console$ sudo apt update
console$ sudo apt install x11-utils
console$ sudo apt install x11vnc
```

### Create/Modify Password

```
console$ x11vnc -storepasswd
```

Example:

```
kickpi@kickpi:~$ x11vnc -storepasswd
Enter VNC password: 
Verify password:    
Write password to /home/kickpi/.vnc/passwd?  [y]/n y
Password written to: /home/kickpi/.vnc/passwd
```

> It can be seen that the password storage path $(passwd_path) is /home/kickpi/.vnc/passwd

### Start the VNC Server

```
console$ export DISPLAY=:0
console$ x11vnc -display :0 -auth /home/kickpi/.Xauthority -rfbport 5900 -rfbauth $(passwd_path)
```

Example:

```
kickpi@kickpi:~$ export DISPLAY=:0
kickpi@kickpi:~$ x11vnc -display :0 -auth /home/kickpi/.Xauthority -rfbport 5900 -rfbauth /home/kickpi/.vnc/passwd
```

## Connect to VNC from Windows

### Download Mobaxterm

Download address of Mobaxterm software: https://mobaxterm.mobatek.net/

### Mobaxterm Operation Steps

1. Create a new session window.
2. Select the session window type as VNC.
3. Enter the IP address.
4. Enter the port number.
5. Start the session window.

![image-20250421141430650](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250421141430650.png)

6. Enter the password and click OK.

![image-20250422152029118](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422152029118.png)
