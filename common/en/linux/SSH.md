# SSH

## Supported Platforms

Debian / Ubuntu

## **User and Password**

The default usernames and passwords for different systems are as follows:

| System | Username | Password |
| ------ | ------ | ------ |
| Debian | linaro | linaro |
| Debian | root | root |
| Ubuntu | kickpi | kickpi |
| Ubuntu | root | root |

## **Using SSH**

Enter the following command in the command line to establish an SSH connection:

```bash
$ ssh <username>@<IP>
```

**Precautions**

- Ensure that the motherboard is properly connected to the network.
- Ensure that the computer can successfully ping the IP address of the motherboard.
- Ensure that the entered username and password exist and are correct.

Connect the board to the network and view the board's IP address through the serial port or the router's background.

**There are three ways to view the IP address after the development board starts:：**

1. **Connect an HDMI monitor, log in to the system, and use the ifconfig eth0 command to view the IP address.**
2. **Enter the ifconfig eth0 command in the debug serial port terminal to view the IP address.**
3. **If there is no debug serial port or HDMI monitor, you can also view the IP address of the development board's network port through the router's management interface. However, many people often cannot see the development board's IP address normally using this method. If you cannot see it, the debugging methods are as follows:**

4. **First, check whether the Linux system has started normally. If the blue light of the development board is flashing, it usually means the system has started normally; otherwise, the system has not started normally.**
5. **Check whether the network cable is plugged in tightly, or try replacing the network cable.**
6. **Try replacing the router (many problems with routers have been encountered, such as the router being unable to assign an IP address normally, or having assigned an IP address normally but not being visible in the router).**
7. **If there is no router to replace, you can only connect an HDMI monitor or use the debug serial port to view the IP address.**


**In addition, it should be noted that the development board's DHCP automatic IP address allocation does not require any settings.**

Example:

```
PS C:\Users\16708> ssh kickpi@192.168.77.186
The authenticity of host '192.168.77.186 (192.168.77.186)' can't be established.
ED25519 key fingerprint is SHA256:635IZrLQdeYlWWl3SCdLxu9fxLEPmStBapj4APCjzZE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.77.186' (ED25519) to the list of known hosts.
kickpi@192.168.77.186's password:
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.1.75 aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

kickpi@kickpi:~$
```

## Common Issues

### Problem with Failed Root User Login

**1. Confirm Configuration Success**

Run the following commands. If the output is `PermitRootLogin yes`, it indicates successful configuration:

```
console$ cat /etc/ssh/ssh_config | grep PermitRootLogin
PermitRootLogin yes
console$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin yes
```

**2. Root User Login Configuration**

Modify the SSH configuration files:

```bash
console$ vim /etc/ssh/ssh_config
+ PermitRootLogin yes
console$ vim /etc/ssh/sshd_config
+ PermitRootLogin yes
console$ sudo /etc/init.d/ssh restart
```