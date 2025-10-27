# LCD

主板对应显示输出

| 主板 | 显示输出接口                     | 多路同显 |
| ---- | -------------------------------- | -------- |
| K1   | HDMI、MIPI DSI、Signal LVDS、EDP |          |
| K1B  |                                  |          |
| K3   |                                  |          |
| K7   | HDMI、USB Type-C DP、MIPI DSI    | 三路     |
| K7C  |                                  |          |
| K8   | HDMI X2、USB Type-C DP、MIPI DSI |          |



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

