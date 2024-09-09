# 01-KICKPI系列板卡简介





## 板卡功能对比

| 功能列表        | K1                                                           | K1B                                                          | K2B                                                          | K3                                                           | K4B               | K5             | K6B             |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------- | -------------- | --------------- |
| PCB尺寸         | 141mm * 80mm                                                 | 81mm * 55mm                                                  | 81mm * 55mm                                                  | 141mm * 80mm                                                 | 81mm * 55mm       | 141mm * 80mm   | 81mm * 55mm     |
| 主控            | Rockchip RK3568B2                                            | Rockchip RK3568B2                                            | Allwinner H618                                               | Rockchip RK3562                                              | Allwinner T113-S3 | Allwinner A133 | Rockchip RV1106 |
| CPU             | ARM A55 X4                                                   | ARM A55 X4                                                   | ARM A53 X4                                                   | ARM Cortex-A53 X4                                            | ARM A7 X2         | ARM A53 X4     | ARM             |
| GPU             | Mali G52 2EE                                                 | Mali G52 2EE                                                 | Mali G31 MP2                                                 | Mali G52 2EE                                                 |                   |                |                 |
| NPU             | RKNN 0.8Tops                                                 | RKNN 0.8Tops                                                 | N                                                            | RKNN 0.8Tops                                                 | N                 | N              | N               |
| 运行内存        | 2GB / 4GB / 8GB                                              | 2GB / 4GB / 8GB                                              | 1GB / 2GB / 4GB                                              | 2GB / 4GB / 8GB                                              | 128MB             | 1GB / 2GB      | 256MB           |
| 存储            | 8GB / 32GB                                                   | 8GB / 32GB                                                   | 8GB / 32GB                                                   | 8GB / 32GB                                                   | 8GB               | 8GB / 32GB     | 8GB             |
| 系统            | * Android13.0<br />* Debian11<br />* Ubuntu20.04             | * Android13.0<br />* Debian11<br />* Ubuntu20.04             | * Android12.0<br />* Ubuntu22.04                             | * Android13.0<br />* Debian11<br />* Ubuntu20.04             | * Buildroot       | * Android10    | * Buildroot     |
| 电源供电        | DC 12V                                                       | Type-C 5V                                                    | Type-C 5V                                                    | DC 12V                                                       | Type-C 5V         | DC 12V         | Type-C 5V       |
| 散热风扇        | Y                                                            | N                                                            | N                                                            | Y                                                            | N                 | Y              | N               |
| LED灯           | * Power LED<br />* Work LED                                  | * Power LED<br />* Work LED                                  | * Power LED<br />* Work LED                                  | * Power LED<br />* Work LED                                  |                   |                |                 |
| USB HOST        | * USB2.0 HOST X1<br />* USB3.0 HOST X1                       | * USB2.0 HOST X1<br />* USB3.0 HOST X1                       | USB2.0 HOST X2                                               | USB2.0 HOST X2                                               |                   |                |                 |
| USB OTG         | USB2.0 OTG X1                                                | USB2.0 OTG X1                                                | USB2.0 OTG X1                                                | USB2.0 OTG X1                                                |                   |                |                 |
| 红外接收器      | Y                                                            | Y                                                            | Y                                                            | Y                                                            | Y                 | Y              | Y               |
| 实时时钟        | HYM8563                                                      | N                                                            | N                                                            | HYM8563                                                      |                   |                |                 |
| 按键            | * Recovery KEY<br />* RESET KEY<br />* MASKROM KEY<br />* POWER KEY | * Recovery KEY<br />* RESET KEY<br />* MASKROM KEY           | * Recovery KEY<br />* RESET KEY<br />* MASKROM KEY           | * Recovery KEY<br />* RESET KEY<br />* MASKROM KEY<br />* POWER KEY |                   |                |                 |
| 扩展引脚        | Extend Pin Total 40Pin：<br />* UART X4<br />* SPI X1<br />* PWM X11<br />* ADC X1<br />* I2C X2<br />* GPIO X28 | Extend Pin Total 20Pin：<br />* UART X2<br />* SPI X1<br />* PWM X1<br />* ADC X1<br />* I2C X1<br />* GPIO X14 | Extend Pin Total 20Pin：<br />* UART X2<br />* SPI X1<br />* PWM X1<br />* ADC X1<br />* I2C X1<br />* GPIO X14 | Extend Pin Total 20Pin：<br />* UART X3<br />* I2C X1<br />* USB2.0 HOST X1<br />* PWM X1<br />* ADC X1<br />* GPIO X11 |                   |                |                 |
| HDMI            | X1（up to 4K@60Hz）                                          | X1（up to 4K@60Hz）                                          | X1（up to 4K@60Hz）                                          | N                                                            |                   |                |                 |
| MIPI DSI LCD    | X2                                                           | X1                                                           | N                                                            | X1                                                           |                   |                |                 |
| Single LVDS LCD | X2                                                           | X1                                                           | N                                                            | X1                                                           |                   |                |                 |
| DUAL LVDS LCD   | N                                                            | N                                                            | N                                                            | X1                                                           |                   |                |                 |
| EDP LCD         | X1                                                           | N                                                            | N                                                            | N                                                            | N                 | N              | N               |
| MIPI CSI摄像头  | 4Lane X1 or 2Lane X2                                         | 4Lane X1 or 2Lane X2                                         | N                                                            | 4Lane X2 or 2Lane X4                                         |                   |                |                 |
| 喇叭            | Max Power 5W                                                 | Max Power 2W                                                 | N                                                            | Max Power 5W                                                 |                   |                |                 |
| 耳机            | Y                                                            | N                                                            | Y                                                            | Y                                                            |                   |                |                 |
| 麦克风          | Y                                                            | N                                                            | N                                                            | Y                                                            |                   |                |                 |
| WIFI/蓝牙       | RTL8822CS                                                    | RTL8822CS                                                    | AW869A                                                       | RTL8822CS                                                    | RTL8723BU         | AW869A         | RTL8723BU       |
| 以太网          | 1000M X2                                                     | 1000M X1                                                     | 1000M X1                                                     | * 1000M X1<br />* 100M X1                                    | 100M X1           | 100M X1        | 100M X1         |
| 4G移动网络      | Y                                                            | N                                                            | N                                                            | Y                                                            | Y                 | Y              | Y               |
| SD卡            | Y                                                            | Y                                                            | Y                                                            | Y                                                            | Y                 | Y              | Y               |
| SATA硬盘        | Y                                                            | N                                                            | N                                                            | N                                                            | N                 | N              | N               |
| M.2硬盘         | Y                                                            | N                                                            | N                                                            | Y                                                            | N                 | N              | N               |





## 技术交流/售后支持

### KICKPI 官方店铺链接

[淘宝店铺链接](https://shop183733283.taobao.com/?spm=a230r.7195193.1997079397.2.10f76f498zHqMG)

[阿里巴巴店铺链接](https://shop122g2107958t7.1688.com/page/index.html?spm=0.0.wp_pc_common_header_companyName_undefined.0)



### 客服QQ号

![image-20231018142716705](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231018142716705.png)



### 技术交流QQ群

![image-20231018142017914](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20231018142017914.png)

