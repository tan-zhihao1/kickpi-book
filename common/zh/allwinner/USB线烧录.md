# USB线烧录

## 获取烧录软件

第一步，从网盘获取烧录工具

``` 
5-DevelopmentTool\USB_ImageBurnTool\PhoenixSuit.zip
```

第二步，解压PhoenixSuit.zip （解压在全英文路径）



## 安装USB驱动

第一步，确认解压后的路径下存在USB驱动目录

```
PhoenixSuit/Drivers/AW_Driver/
```

> 驱动不支持直接打开安装，必须在设备管理器中 识别出未知设备后才能安装驱动

第二步，主板进入烧录模式；

第三步，打开电脑设备管理器界面，选择未知设备，右键，点击更新驱动程序软件；

（若主板成功进入烧录模式，未安装USB驱动时，可识别出`未知设备`）

![image-20221129104615672](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104615672.png)

第四步，选择浏览计算机以查找驱动程序软件；

![image-20221129104636689](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104636689.png)

第五步，选择 PhoenixSuit 路径下的 AW_Driver 文件，点击确定；

![image-20221129104649695](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104649695.png)

第六步，选中包括子文件夹，点击下一步；

![image-20221129104706378](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20221129104706378.png)

第七步，安装USB驱动成功，显示识别的USB设备；

![image-20241204152947830](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241204152947830.png)





## 通用工具烧录镜像

第一步，解压PhoenixSuit.zip

第二步，打开PhoenixSuit.exe

![image-20240306170759670](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170759670.png)

第三步，进入烧录工具界面，点击一键刷机；

![image-20240306170835586](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306170835586.png)



第四步，在一键刷机界面，点击浏览，选择要烧录的镜像；***不要点击立即升级！！！***

![image-20240306171236404](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171236404.png)

第五步，主板重新进入烧录模式，等待弹出烧录提示，点击是进行烧录

![image-20240306171455427](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20240306171455427.png)

![image-20250909114121321](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250909114121321.png)

第六步，PhoenixSuit 烧录完成板卡会自动重启



## 产线工具烧录镜像

第一步，网盘获取产线烧录工具，解压；

```
5-DevelopmentTool\FactoryTool\PhoenixUSBPro.zip
```

第二步，进入解压后的文件夹，打开 PhoenixUSBPro.exe；

![image-20250728101724365](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250728101724365.png)

第三步，点击固件包，选择要烧录的镜像；

![image-20250728101927092](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250728101927092.png)

第四步，点击启动，观察绿灯亮；

![image-20250728102113315](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250728102113315.png)

第五步，主板进入烧录模式，工具会识别到新设备，红灯亮；（此时请勿拔插设备！！！）

第六步，等待绿灯亮，重复插入设备，进入烧录模式；

> 批量烧录建议陆续接入设备，等待全部烧录成功后，再统一拔出；

第七步，等待烧录进度条完成；（烧录后，主板会进入关机状态）

第八步，点击POWER键开机，或重新插拔电源；



## 常见问题

1.遇到兼容性错误

![image-20241026114145257](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241026114145257.png)

解决方法：

参考Microsoft解决方法

Microsoft官方链接

https://support.microsoft.com/zh-cn/windows/%E6%97%A0%E6%B3%95%E5%9C%A8%E6%AD%A4%E8%AE%BE%E5%A4%87%E4%B8%8A%E5%8A%A0%E8%BD%BD%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F-8eea34e5-ff4b-16ec-870d-61a4a43b3dd5

![image-20241026114443290](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241026114443290.png)

2.烧写固件失败


![image-20250721173420273](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250721173420273.png)

烧录软件使用管理员和兼容性模式打开