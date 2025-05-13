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

