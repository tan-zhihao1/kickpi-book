# 02-USB线烧录

此文档用于用户烧录或升级主板系统。



## 通用镜像烧录

### 安装USB驱动<a id="USBDRV"></a>

**网盘路径**

```
5-DevelopmentTool/USB Driver USB/DriverAssitant_v5.13.zip
```

> Android14.0的USB驱动 DriverAssitant 需要更新到V5.1.1版本。



1. 将 DriverAssitant_v5.13.zip 解压，打开解压后的文件夹；

2. 找到 DriverInstall.exe ，双击运行；


![image-20241025101533688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101533688.png)



3. 点击驱动安装，提示安装成功；

（若已安装旧版本驱动，可先进行驱动卸载再驱动安装，保持驱动版本为最新）

![image-20241025101606583](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101606583.png)



### 安装RKDevTool

**网盘路径**

```
5-DevelopmentTool/USB ImageBurnTool/RKDevTool_v3.30_for_window.zip
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





## ARMBIAN镜像烧录

由于 armbian 的镜像与 rockchip 官方的镜像有所区别，烧录方式不能沿用。





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

