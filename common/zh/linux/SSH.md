# SSH

## 支持平台

Debian / Ubuntu



## **用户及密码**

不同系统的默认用户名和密码如下：

| 系统   | 用户名 | 密码   |
| ------ | ------ | ------ |
| Debian | linaro | linaro |
| Debian | root   | root   |
| Ubuntu | kickpi | kickpi |
| Ubuntu | root   | root   |



## **SSH使用**

在命令行中输入以下命令进行 SSH 连接：

```bash
$ ssh <username>@<IP>
```

**注意事项**

- 确保主板网络连接正常。
- 确保电脑能够正常 ping 通主板的 IP 地址。
- 确保输入的用户名和密码存在且正确。

**开发板启动后查看IP地址有三种方法：**

1. **接显示屏，然后登录系统使用ifconfig eth0命令查看IP地址**
2. **在调试串口终端输入ifconifg eth0命令来查看IP地址**
3. **如果没有调试串口，也没有屏幕，还可以通过路由器的管理界面来查看开发板网口的IP地址。不过这种方法经常有人会无法正常看到开发板的IP地址。如果看不到，调试方法如下所示：**

4. **首先检查Linux系统是否已经正常启动，如果开发板的蓝灯闪烁了，一般是正常启动了，否则说明系统都没正常启动；**
5. **检查网线有没有插紧，或者换根网线试下；**
6. **换个路由器试下（路由器的问题有遇到过很多，比如路由器无法正常分配IP地址，或者已正常分配IP地址但在路由器中看不到）；**
7. **如果没有路由器可换就只能连接屏幕或者使用调试串口来查看IP地址。**

**另外需要注意的是开发板DHCP自动分配IP地址是不需要任何设置的。**

示例：

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



## 常见问题

### ROOT用户登陆失败问题

**1、确认配置是否成功**

运行以下命令，若输出结果为 `PermitRootLogin yes`，则表示配置成功：

```
console$ cat /etc/ssh/ssh_config | grep PermitRootLogin
PermitRootLogin yes
console$ cat /etc/ssh/sshd_config | grep PermitRootLogin
PermitRootLogin yes
```

**2、root用户登陆配置**

修改 SSH 配置文件：

```bash
console$ vim /etc/ssh/ssh_config
+ PermitRootLogin yes
console$ vim /etc/ssh/sshd_config
+ PermitRootLogin yes
console$ sudo /etc/init.d/ssh restart
```

