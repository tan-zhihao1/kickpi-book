# 10-Image Burning



## Instructions

SDK - refers to the source code. For the specific name, please refer to the SDK Source Code Compilation.



## Image Burning

### Path of AndroidTool

The tool can be found in the source code. A backup copy is available in the cloud disk under 5 - Development Tool.
The USB driver of Android 14.0, DriverAssitant, needs to be updated to version V5.1.1. You can refer to the tool section below for the update.
For the RK3576 platform, the Windows firmware burning tool must be version V3.28 or above.
Windows Burning Tool，path in the cloud disk:

```
5-DevelopmentTool
```



### Installing the USB Driver

**USB Driver Path**

```
5-DevelopmentTool/USB Driver/DriverAssitant_v5.13.zip
```

1. Unzip DriverAssitant_v5.13.zip and open the unzipped folder.

2. Find DriverInstall.exe and double - click to run it.

![image-20241025101533688](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101533688.png)

3. Click "Install Driver" and a prompt indicating successful installation will appear.

![image-20241025101606583](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101606583.png)



**USB设备验证**

1. 主板上电，准备一个能传输文件的 type-c 线
2. 使用 type-c 连接主板和PC电脑

3. 打开电脑中设备管理器

4. 可以查看到usb设备，说明USB驱动安装成功

![image-20241025101105194](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025101105194.png)





**不识别问题**

1. 确认主板已经上电

2. 打开DriverInstall.exe ，尝试驱动卸载，再进行驱动安装

3. 反馈咨询客服



### 使用烧录工具

调试烧录工具

```
5-DevelopmentTool/USB ImageBurnTool 镜像烧录工具/RKDevTool_v3.30_for_window.zip
```

解压 RKDevTool_v3.30_for_window.zip 压缩文件，在文件下找到 RKDevTool.exe ，双击打开

![image-20241025161627415](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025161627415.png)



#### **整包烧录**

LOADER / MASKROM模式

```
LOADER模式：可分区烧录，分区烧录，详情见更多说明
MASKROM模式：不可分区烧录
```

1. `LOADER模式`按下RECOVERY按键，上电 （或者按复位进行复位）

`MASKROM模式`按下板子背后的MASKROM按键，上电 （或者按复位进行复位）

2. 瑞芯微开发工具上能够识别到LOADER设备或MASKROM设备

3. 点击升级固件，跳转到升级固件界面

4. 点击固件，选择要烧录的镜像 update-*.img

5. 点击升级，等待烧录完成

6. 重启即可



#### **更多说明**

烧录工具更多详细说明见

```
/RKDevTool_v3.30_for_window/开发工具使用文档_v1.0.pdf
```



## 工厂烧录

```
5-DevelopmentTool开发工具/FactoryTool 工厂镜像烧录工具/FactoryTool_v1.89.zip
```
打开软件

![image-20250109172029374](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172029374.png)

选择固件，进行烧录，接入主板后，使主板进入 MASKROM / LOADER 模式

![image-20250109172203575](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250109172203575.png)
