# Armbian_SDK编译

## SDK介绍

内核版本：6.1

Debian系统版本：12

Ubuntu系统版本：2404

> 此 SDK 的库和源码源于国外不同的网站网址，国内用户编译此 SDK 必须确保网络能够访问外网！！！



## 获取SDK源码

* 网盘目录

```
1-SDK/Armbian/
├── kickpi-armbian.md5sum
└── kickpi-armbian.tar.gz
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
$ tar -zxvf md5sum rk3576-linux-*.tar.gz -C rk3576-armbian/
$ cd rk3576-armbian/
$ git reset --hard
```



## 图形化界面编译

```
SDK$ ./compile.sh
```

> 运行后，会进入图形化选择编译界面。
>
> 图形化界面配置须知：
>
> * 方向键选择配置，Enter 键确认配置，空格键选中或取消选中。
> * 支持首字母快速检索，可键盘按下首字母，会跳转到对应首字母选项。

### 通用配置

1. 选择不修改 Kernel 配置

![image-20250704140210097](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704140210097.png)

2. 选中显示 CSC / WIP / EOS / TVB 相关配置（目前 K7 在CSC配置中）

![image-20250704140436194](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704140436194.png)

3. 同意警告声明

![image-20250704140605228](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704140605228.png)

4. 向下选中 kickpi-k7 配置

![image-20250704140728595](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704140728595.png)

5. 选择 6.1 Kernel 版本（目前仅有此选项）

![image-20250704140947103](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704140947103.png)

### Debian/Ubuntu系统选择

6. 选择 **Bookworm** 或 **Ubuntu noble 24.04** 版本（带 supported 选项 Armbian 官方支持，使用比较稳定！）

![image-20250704141035573](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704141035573.png)

### 桌面版/服务器版选择

7. 选择系统配置，桌面版选择 desktop environment / 服务器版选择 console interface (server)

![image-20250704141221587](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704141221587.png)

### 桌面版配置选择

8.1 若第7步选择桌面版，桌面版提供了四种可支持的桌面，根据需要选择。

> 注意： Ubuntu 系统选择 gnome 桌面，目前仅验证了此桌面！

![image-20250704141508062](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704141508062.png)

9. 系统基础包配置

![image-20250704141809333](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704141809333.png)

10. 对应类型的系统基础包选择 （空格选中或者取消选中，选中的包会自动编译进系统）

![image-20250704142123550](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704142123550.png)



## 命令编译（非图形化界面）

在图形化界面选择编译后，可通过打印信息查看到对应命令行编译指令。

示例：

下面是在编译 K7 ubuntu 24.04 系统 gnome 桌面时，截取到的打印。

![image-20250704142554606](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704142554606.png)

则获取到以下命令

```
SDK$ ./compile.sh build BOARD=kickpi-k7 BRANCH=vendor BUILD_DESKTOP=yes BUILD_MINIMAL=no DESKTOP_APPGROUPS_SELECTED= DESKTOP_ENVIRONMENT=gnome DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base KERNEL_CONFIGURE=no RELEASE=noble 
```

> 执行该命令，会编译 K7 ubuntu 24.04 系统 gnome 桌面（无需进图形化界面重新配置）



## 单独编译

**kernel 单编**

```
$ ./compile.sh kernel BOARD=kickpi-k7 BRANCH=vendor
```



**dts 单编**

```
$ ./compile.sh kernel-dtb BOARD=kickpi-k7 BRANCH=vendor
```

板卡上 dtb 替换路径

```
/boot/dtb/rockchip/rk3576-kickpi-k7.dtb
```



**Kernel config 配置**

```
./compile.sh kernel-config BOARD=kickpi-k7 BRANCH=vendor
```



## **Armbian官方编译指导**

https://docs.armbian.com/Developer-Guide_Build-Commands/