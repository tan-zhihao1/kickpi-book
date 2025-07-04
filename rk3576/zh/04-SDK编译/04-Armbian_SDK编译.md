# Armbian_SDK编译

## SDK介绍

内核版本：6.1

Debian系统版本：12

Ubuntu系统版本：2404



### 获取SDK源码

* 网盘目录

```

```

>  *这里一般指代日期，下面同，实际名称以网盘为准

1. 拷贝源码压缩包到编译主机目录（若编译主机为虚拟机，不可放在共享目录下编译）

2. MD5校验文件完整性

```
md5sum rk3576-Armbian-*.tar.gz
```

> 判断结果和 rk3576-linux-*.md5sum 内容一致，则文件下载完整

3. 解压源码压缩包

```
$ mkdir rk3576-armbian
$ tar -zxvf md5sum rk3576-linux-*.tar.gz -C rk3576-linux/
$ cd rk3576-linux/
$ git reset --hard
```

