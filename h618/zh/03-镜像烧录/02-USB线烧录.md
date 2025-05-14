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



**主板进入烧录模式方法**

先按住板子背面的MASKROM按键 再连接TYPE-C口和电脑。

![image-20250509191757297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191757297.png)







**主板进入烧录模式**

操作步骤：

1. 按住板子背面的maskrom按键
2. 主板上电，如果已上电则需重新上电（按一下复位键）

3. 复位后，烧录键三秒左右后松开

![image-20250509191757297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509191757297.png)





### Android桌面界面

主板连接HDMI显示器，系统启动成功后，界面如下

![image-20240306171855522](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171855522.png)