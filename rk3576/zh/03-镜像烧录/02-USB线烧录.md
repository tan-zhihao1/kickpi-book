# 02-USB线烧录

此文档用于用户烧录或升级主板系统。

镜像烧录章节，介绍烧录镜像的环境、工具和操作步骤。

工厂烧录章节，介绍批量烧录的环境、工具和操作步骤。（目前不支持Armbian批量烧录）



## 镜像烧录

### 获取镜像文件

下载网盘上日期较新的镜像文件。

通用镜像烧录，如：`update-rk3576-kickpi-k7-android-multi-202505291812.img`。

Armbian镜像烧录，如：`armbian.cfg`、`MiniLoaderAll.bin`、`Armbian-unofficial_25.08.0-trunk_Kickpi-k7_noble_vendor_6.1.115_gnome_desktop.img`。

[点击查看镜像文件的网盘位置以及镜像介绍。](01-获取镜像文件.md)



### 安装USB驱动<a id="USBDRV"></a>

**网盘路径**

```
5-DevelopmentTool/USB Driver USB/DriverAssitant_v5.13.zip
```

> Android14.0的USB驱动 DriverAssitant 需要更新到V5.1.1以上的版本。

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



### 通用烧录镜像

1. 使主板进入 LOADER 模式或者 MASKROM 模式；

   > LOADER / MASKROM模式均支持整包烧录，选择一个模式进行烧录即可。
   >
   > * 按下网口旁边的 RECOVERY 按键，给主板重新上电，系统进入 LOADER 模式，松开按键；
   >
   > * 按下板子背后的 MASKROM 按键，给主板重新上电，系统进入 MASKROM 模式，松开按键；

   

2. 瑞芯微开发工具上能够识别到 LOADER 设备或 MASKROM 设备；（需要先安装USB驱动，才能识别到相关设备）

![image-20250422093529751](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093529751.png)



3. 点击 **Upgrade Firmware**，跳转到升级固件界面；

   ![image-20250818154842113](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250818154842113.png)

4. 点击 **Firmware**，选择要烧录的镜像 update-*.img；[获取镜像文件](01-获取镜像文件.md)

![image-20250818154913407](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250818154913407.png)

5. 点击 **Upgrade**，等待烧录完成；

![image-20250422093623591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093623591.png)



6. 烧录完成；

![image-20250422093934098](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422093934098.png)

> 注意事项：
>
> 烧录后首次开机，必须等待机器正常开启，画面正常后再进行断电操作！
>
> 首次开机异常断电会导致系统异常，系统变砖！



### Armbian 镜像烧录

> 由于 armbian 的镜像与 rockchip 官方的镜像有所区别，烧录方式不能沿用 ！

1. 使主板进入 LOADER 模式或者 MASKROM 模式；

   > LOADER / MASKROM模式均支持整包烧录，选择一个模式进行烧录即可。
   >
   > * 按住网口旁边的 RECOVERY 按键，给主板重新上电，系统进入 LOADER 模式；
   >
   > * 按住板子背后的 MASKROM 按键，给主板重新上电，系统进入 MASKROM 模式；

2. 瑞芯微开发工具上能够识别到 LOADER 设备或 MASKROM 设备；（需要先安装USB驱动，才能识别到相关设备！）

3. 点击 **Download Image**，跳转到分区升级固件界面；

   ![image-20250704101553072](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704101553072.png)

4. 在分区框内，**鼠标右键**打开配置选项，点击 **Load Config**，选择 **armbian.cfg** 文件（cfg格式文件）； [获取镜像文件](01-获取镜像文件.md)

   ![image-20250704101726253](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704101726253.png)

5. 选择 Loader 分区配置，点击对应文件选择方框，选择 **MiniLoaderAll.bin** 文件（bin格式文件）；[获取镜像文件](01-获取镜像文件.md)

![image-20250704103334992](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704103334992.png)

6. 选择 Image 分区配置，点击对应文件选择方框，选择 Armbian 镜像文件（img格式文件）；[获取镜像文件](01-获取镜像文件.md)

   ![image-20250704104346690](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704104346690.png)

7. 勾选分区烧录配置以及 **Write by Address** 配置（必须选取勾选！）

![image-20250704104207269](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704104207269.png)

8. 点击 Run，等待烧录完成；

![image-20250704104936954](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250704104936954.png)

> 注意事项：
>
> 烧录后首次开机，必须等待机器正常开启，画面正常后再进行断电操作！
>
> 首次开机异常断电会导致系统异常，系统变砖！



## 工厂烧录

> 不支持 Armbian 系统批量烧录

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

4. 使主板进入 **LOADER模式** 或者 **MASKROM模式**；

   > LOADER / MASKROM模式均支持整包烧录，选择一个模式进行烧录即可。
   >
   > * 按住网口旁边的 RECOVERY 按键，给主板重新上电，系统进入 LOADER 模式；
   >
   > * 按住板子背后的 MASKROM 按键，给主板重新上电，系统进入 MASKROM 模式；

5. 观察灯，当识别到设备会切换成红灯，此时不要拔出或接入设备；

   ![image-20250422103006729](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250422103006729.png)

   

6. 等待绿灯，重复步骤3、步骤4、步骤5，可同时烧录新的设备；

7. 烧录所有设备完成，点击 **Stop**；



## 常见问题

### **Loader或Maskrom模式不识别问题**

1. 进入**LOADER模式**需要按下RECOVERY按键，上电（主板已上电可按复位进行复位）；进入**MASKROM模式**需要按下板子背后的MASKROM按键，上电（主板已上电可按复位进行复位）。

2. 打开电脑中设备管理器；

3. 可以查看到usb设备，说明USB驱动安装成功；

![image-20241025101105194](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101105194.png)



如果识别不到 Class for rockusb devices，可尝试下面步骤：

1. 打开DriverInstall.exe ，尝试驱动卸载，再进行驱动安装；[USB驱动安装](#USBDRV)

2. 反馈咨询客服



### **烧录工具的其他功能使用**

烧录工具更多说明见

```
RKDevTool_v3.30_for_window/开发工具使用文档_v1.0.pdf
```



### Armbian 镜像烧录失败问题

如果遇到反复启动 DDR 信息失败时，说明之前的 Armbian 镜像烧录失败导致系统异常。

![image-20250917165634041](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250917165634041.png)

解决办法：

第一步，烧录 android / linux 的整包镜像，恢复系统。

第二部，重新进行 armbian 镜像烧录即可。

