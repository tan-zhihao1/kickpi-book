# 02-USB线烧录

## 1. Android USB线 镜像烧录

镜像可通过网盘这个路径下载

``` 
h618_data\2-Image\K2*\Android12.0_*
```



### 准备工具

**下载烧录工具**

```
h618_data\5-DevelopmentTools\USB_ImageBurnTool\PhoenixSuit.zip
```

**硬件准备工具**

准备一根 USB A to C 烧录线，用于连接烧录主机 于 开发板



### 安装USB驱动

**解压PhoenixSuit.zip**

USB驱动目录

```
PhoenixSuit/Drivers/AW_Driver/
```

> 驱动不支持直接打开安装，必须在设备管理器中 识别出未知设备后才能安装驱动



**主板进入烧录模式**

按住板子背面的MASKROM按键 再上电。

> 详细教程如下面

![image-20250509191757297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191757297.png)



**打开电脑设备管理器界面**

若主板成功进入烧录模式，并且电脑不具备USB驱动时，可识别出`未知设备`

按照下图步骤所示，操作安装驱动即可

![image-20221129104615672](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104615672.png)



![image-20221129104636689](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104636689.png)



![image-20221129104649695](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104649695.png)



![image-20221129104706378](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104706378.png)

![image-20241204152947830](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241204152947830.png)



### 常见问题

1.遇到兼容性错误

![image-20241026114145257](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241026114145257.png)

解决方法参考Microsoft解决方法

Microsoft官方链接

https://support.microsoft.com/zh-cn/windows/%E6%97%A0%E6%B3%95%E5%9C%A8%E6%AD%A4%E8%AE%BE%E5%A4%87%E4%B8%8A%E5%8A%A0%E8%BD%BD%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F-8eea34e5-ff4b-16ec-870d-61a4a43b3dd5

![image-20241026114443290](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241026114443290.png)

### 安装镜像烧录工具

**解压PhoenixSuit.zip**



**打开PhoenixSuit.ext**

![image-20240306170759670](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170759670.png)



**烧录工具界面**

![image-20240306170835586](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170835586.png)



### 烧录镜像

**选择烧录镜像**

1. 进入 **一键刷机** 界面
2. 点击 **浏览** 选择镜像

![image-20240306171236404](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171236404.png)



**主板进入烧录模式**

操作步骤：

1. 按住板子背面的maskrom按键
2. 主板上电，如果已上电则需重新上电（按一下复位键）

3. 复位后，烧录键三秒左右后松开

![image-20250509191757297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191757297.png)



**开始烧录镜像**

1. 点击 `是` 开始烧录镜像

2. 等待烧录进度条完成
3. 烧录完成后，主板自动重启

![image-20240306171455427](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171455427.png)



### Android桌面界面

主板连接HDMI显示器，系统启动成功后，界面如下

![image-20240306171855522](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171855522.png)