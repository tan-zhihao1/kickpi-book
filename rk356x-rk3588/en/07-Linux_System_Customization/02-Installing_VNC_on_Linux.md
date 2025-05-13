# 02-Installing_VNC_on_Linux

VNC (Virtual Network Computing) is a graphical desktop sharing system based on the Remote Frame Buffer protocol (RFB). It allows users to connect to a remote computer over the network and operate the remote computer's desktop as if it were a local one.

> SDK$ - Hereinafter refers to the source code path.
>
> console$ - Hereinafter generally refers to the motherboard command-line console.
>
> ADB$ - Android Debug Bridge command-line tool. Hereinafter generally refers to an environment where ADB can be run.

## Configuring the VNC Server on the Motherboard

### Installing the VNC Service

```
console$ sudo apt-get install tigervnc-standalone-server tigervnc-common -y
```

### Setting the VNC Password

Execute the `vncpasswd` command to set the VNC password. This is not the password for the user account but is specifically used for VNC clients to connect to the VNC server.

```console
console$ vncpasswd
```

Remember the password for later use when connecting the client.

### Configuring Desktop Startup

Create a file named `$HOME/.vnc/xstartup` with the following content:

```console
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
exec startxfce4
```

Ensure that the `xstartup` file has executable permissions.

```console
console$ chmod +x $HOME/.vnc/xstartup
```

### Setting up the systemd Service to Start and Stop VNC

To create a systemd service to start the VNC server, create the file `/etc/systemd/system/vncserver@.service`. Use `sudo` or log in as the `root` user since this directory is read-only.

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

The following commands are for using `systemd`.

To start the VNC service:

```console
sudo systemctl start vncserver@1.service
```

To stop the VNC service:

```console
sudo systemctl stop vncserver@1.service
```

To restart the VNC service:

```console
sudo systemctl restart vncserver@1.service
```

## Connecting to VNC from Windows

### Downloading MobaXterm

Download MobaXterm from the following link: https://mobaxterm.mobatek.net/

### MobaXterm Operation Steps

1. Create a new session window.
2. Select the session window type as VNC.
3. Enter the IP address.
4. Enter the port number (5901).
5. Start the session window.

![image-20250428102638564](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428102638564.png)

6. Enter the password you set with `vncpasswd` and click OK.

![image-20250422152029118](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422152029118.png)