# MIPI转接板介绍

本章主要介绍 KICKPI 开发板使用的 MIPI转接板。

为了主板能够接入不同形式、规格的 MIPI 屏及触摸框，通过转接板的形式适配各式各样的触摸接口。



## V1.0 MIPI 转接板

> V1.0 仅支持 rockchip 方案主板 （K1 / K1B / K3 / K7 / K8）

接口1：40 Pin 屏接口（通过 FPC 排线连接 MIPI 屏 + I2C 触摸框）

接口2：10 Pin 触摸接口（通过 FPC 排线连接 I2C 触摸框接口）

接口3：30 Pin 屏接口（通过 FPC 排线连接主板接口）

![image-20250707145025437](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250707145025437.png)



## V1.3 MIPI 转接板

> V1.3 MIPI 转接板添加了全志系列主板的支持，并扩展了 6 PIN的 I2C 触摸框支持。
>
> 瑞芯微系列主板与转接板的接口4进行连接！（K1 / K1B / K3 / K7 / K8）
>
> 全志系列主板与转接板的接口5进行连接！（K4B / K5 / K5C ）

接口1：40 Pin 屏接口（通过 FPC 排线连接 MIPI 屏 + I2C 触摸框）

接口2：6 Pin 触摸接口（通过 FPC 排线连接 I2C 触摸框接口）

接口3：10 Pin 触摸接口（通过 FPC 排线连接 I2C 触摸框接口）

接口4：30 Pin 屏接口（通过 FPC 排线连接 rockchip 瑞芯微系列主板接口）

接口5：30 Pin 屏接口（通过 FPC 排线连接 allwinner 全志系列主板接口）

![image-20250707145122477](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250707145122477.png)



## 常见问题

### 购买了一块MIPI屏幕和瑞芯微和全志的两块主板，怎么两块主板都能点亮屏幕？

第一步，确认屏幕的转接板是 V1.3 版本。

第二步，根据主板选择接入不同的 30 Pin 接口。



> 操作前请先将主板断电，请勿带电操作！！！
