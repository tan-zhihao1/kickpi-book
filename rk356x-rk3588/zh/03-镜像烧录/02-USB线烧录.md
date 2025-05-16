# 02-USB线烧录

此文档用于用户烧录或升级主板系统。



## 镜像烧录

### 安装USB驱动<a id="USBDRV"> </a>

**网盘路径**

```
5-DevelopmentTool/win_x64_UsbDriver/DriverAssitant_v5.13.zip
```



1. 将 DriverAssitant_v5.13.zip 解压，打开解压后的文件夹；

2. 找到 DriverInstall.exe ，双击运行；


![image-20241025101533688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101533688.png)



3. 点击驱动安装，提示安装成功；

（若已安装旧版本驱动，可先进行驱动卸载再驱动安装，保持驱动版本为最新）

![image-20241025101606583](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101606583.png)



### 安装RKDevTool

**网盘路径**

```
5-DevelopmentTool/win_x64_UsbImageBurnTool/RKDevTool_v3.30_for_window.zip
```

> RK3576平台windows固件烧写工具必须使用V3.28及以上版本。



1. 将 RKDevTool_v3.30_for_window.zip 解压，打开解压后的文件夹；

2. 找到 RKDevTool.exe，双击运行；

![image-20241025161627415](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025161627415.png)



### 烧录镜像

1. 进入 **LOADER模式** 需要按下RECOVERY按键，上电（主板已上电可按复位进行复位）；进入 **MASKROM模式** 需要按下板子背后的MASKROM按键，上电（主板已上电可按复位进行复位）；

   > LOADER / MASKROM模式均支持整包烧录，选择一个模式进行烧录即可。

2. 瑞芯微开发工具上能够识别到 LOADER设备 或 MASKROM设备；（需要先安装USB驱动，才能识别到相关设备）

![image-20250422093529751](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093529751.png)



3. 点击 **Upgrade Firmware**，跳转到升级固件界面；

4. 点击 **Firmware**，选择要烧录的镜像 update-*.img；[获取镜像文件](01-获取镜像文件.md)

![image-20250422093506005](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093506005.png)



5. 点击 **Upgrade**，等待烧录完成；

![image-20250422093623591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093623591.png)



6. 烧录完成；

![image-20250422093934098](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093934098.png)



## 工厂烧录

### 安装FactoryTool

**网盘路径**

```
5-DevelopmentTool/FactoryTool/FactoryTool_v1.89.zip
```

1. 将 FactoryTool_v1.89.zip 解压，打开解压后的文件夹；

2. 找到 FactoryTool.exe，双击运行；

![image-20250109172029374](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172029374.png)



### 烧录镜像

1. 点击 **Firmware**，选择要烧录的镜像 update-*.img；

![image-20250422101009222](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422101009222.png)



2. 点击 **Run**，启动工厂烧录模式；

3. 绿灯状态下，使用TYPE-C连接主板；

4. 进入 **LOADER模式** 需要按下RECOVERY按键，上电（主板已上电可按复位进行复位）；进入 **MASKROM模式** 需要按下板子背后的MASKROM按键，上电（主板已上电可按复位进行复位）；[USB驱动安装](#USBDRV)

5. 观察灯，当识别到设备会切换成红灯，此时不要拔出或接入设备；

   ![image-20250422103006729](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422103006729.png)

   

6. 等待绿灯，重复步骤3、步骤4、步骤5，可同时烧录新的设备；

7. 烧录所有设备完成，点击 **Stop**；



## 常见问题

**Loader或Maskrom模式不识别问题**

1. 进入**LOADER模式**需要按下RECOVERY按键，上电（主板已上电可按复位进行复位）；进入**MASKROM模式**需要按下板子背后的MASKROM按键，上电（主板已上电可按复位进行复位）。

2. 打开电脑中设备管理器；

3. 可以查看到usb设备，说明USB驱动安装成功；

![image-20241025101105194](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101105194.png)



如果识别不到 Class for rockusb devices，可尝试下面步骤：

1. 打开DriverInstall.exe ，尝试驱动卸载，再进行驱动安装；[USB驱动安装](#USBDRV)

2. 反馈咨询客服



**烧录工具的其他功能使用**

烧录工具更多说明见

```
RKDevTool_v3.30_for_window/开发工具使用文档_v1.0.pdf
```



## 1. 镜像烧录

开发板出厂默认烧录好镜像，若不需要更换系统的话，可直接跳过此步骤，启动系统



### 下载准备工具

镜像烧录 准备工具如下所示

```
├── System Image 系统镜像
│   └── RK3568-K1
│       ├── Android13
│       ├── Debian11
│       └── Ubuntu2004
└── Development Tools 开发工具
    ├── USB Driver USB驱动
    └── USB Image Burn Tool 镜像烧录工具
```

> 镜像烧录 必须下载 USB驱动、镜像烧录工具
>
> 系统镜像 可根据实际需要选择下载 或 自行通过SDK编译



### 安装USB驱动

USB驱动网盘路径

```
DevelopmentTools开发工具\USB驱动\DriverAssitant_v5.12.zip
```

![image-20230526154517334](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20230526154517334.png)



### 获取镜像文件

镜像文件可通过以下两种方式获取：网盘下载、SDK编译

SDK编译详细步骤参考文档 [SDK源码编译](11-SDK源码编译.md)

镜像文件网盘目录：

```
├──Image系统镜像
    └── RK3568-K1
        ├── Android13
        	├── update-rk3568-kickpi-k1-android-hdmi-2023102403.img
        ├── Debian11
        └── Ubuntu2004
```



镜像名称解析

![image-20231024111614065](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231024111614065.png)



### 安装镜像烧录工具

镜像烧录工具网盘目录

```
DevelopmentTools开发工具\USB镜像烧录工具\RKDevTool_v3.30.zip
```

1. 通过网盘下载镜像烧录工具后，解压文件夹，得到以下目录列表

![image-20241025184222743](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025184222743.png)

> 工具使用请细看开发工具使用文档。

2. 修改镜像烧录工具语言（可选）

默认镜像烧录工具为中文界面，可按照如下所示修改为英文界面

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010202819610.png)

![img](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231010203036869.png)



3. 打开烧录工具

![image-20231017114016561](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231017114016561.png)

4. 烧录工具界面如下

![image-20250428105459703](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105459703.png)



### 开发板进入烧录模式(Pic)<a id="burn_mode"> </a>

烧录镜像 必须使开发板进入烧录模式，Rockchip平台的烧录模式有LOADER模式、Maskrom模式

LOADER模式 常用于完整镜像烧录、分区烧录皆可

Maskrom模式 常用于系统损坏后，强制烧录镜像

> 按键可通过KICKPI-硬件介绍，开发板信息章节进行查看



操作步骤：

1. Window电脑上打开烧录工具（参考以上说明安装烧录工具）

2. 开发板OTG接口 连接 电脑USB接口

3. 保持长按 Loader按键，并且连接DC电源供电接口进行上电操作(若已上电可按复位键进行复位操作)

4. 查看 镜像烧录工具 是否正常识别Loader设备/Maskrom设备

5. 若正常识别到设备，则可松开Loader按键，否则重复上述步骤多尝试几次

> 进入Maskrom模式，重复操作步骤，注意第3步长按板子背面的MASKROM按键，非Loader按键



### 烧录镜像

烧录镜像 分为 完整镜像烧录 和 分区镜像烧录

完整镜像烧录 常用于刷机 或 开发板更换不同系统

分区镜像烧录 常用于调试阶段，必须明确每个分区的作用再进行烧录，否则会导致系统损坏



#### 烧录完整镜像

![image-20250428105322719](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105322719.png)

操作步骤：

1. 进入完整镜像烧录页面
2. 选择镜像路径
3. 烧录工具成功识别板卡设备[进入烧录模式](#burn_mode)
4. 开始烧录镜像
5. 查看 烧录日志，烧录成功后自动重启进入系统



#### 烧录分区镜像

分区镜像主要用于正常编译的镜像的调试使用，多屏自适应镜像不能烧分区镜像替换，网盘中的固件均为多屏自适应镜像。

![image-20250428105212038](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250428105212038.png)

操作步骤：

1. 进入分区镜像烧录页面
2. 勾选烧录分区项 (其中Parameter一定需要勾选)
3. 选择对应文件存放的路径
4. 烧录工具成功识别板卡设备[进入loader模式](#burn_mode)
5. 开始烧录镜像
6. 查看 烧录日志，烧录成功后自动重启进入系统





