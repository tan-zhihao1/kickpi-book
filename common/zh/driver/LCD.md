# LCD

RK3568 配备四路显示输出接口，分别为 HDMI、MIPI、LVDS、EDP，其中最高支持三路同时显示输出。

RK3576 配备三路显示输出接口，分别为 HDMI、USB Type-C DP、MIPI DSI。



|        |                               |      |
| ------ | ----------------------------- | ---- |
|        |                               |      |
| RK3576 | HDMI、USB Type-C DP、MIPI DSI | dan  |
|        |                               |      |



## 文档参考

Android_SDK

```
RKDocs/common/display/
```

Linux_SDK

```
docs/cn/Common/DISPLAY/
docs/en/Common/DISPLAY/
```



## DTS

**MIPI DSI**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-5-720-1280-F050008M01.dtb
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-8-800-1280-MX080B2140.dtb
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-10.1-800-1280-MX101BA1340.dtb
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-android-mipi-10.1-800-1280-AT101DS40I.dtb
```



**HDMI**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-lcd-hdmi.dtsi
```



**TYPE-C DP**

```
kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-lcd-dp.dtsi
```

