# CAMERA

## 环境

K7主板版本 >= 2.0。

摄像头 imx415，4 lane 22 pin 接口。

MIPI_CS0、MIPI_CS1、MIPI_CS3 为 22 pin 的 MIPI_CSI_RX 接口。



## dts配置

添加摄像头配置，cam0 ~ cam3 对应原理图的 MIPI-CSI_0 ~ MIPI-CSI_3。

> kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam0.dtsi 对应 MIPI-CSI_0
>
> kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam1.dtsi 对应 MIPI-CSI_1
>
> kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7-cam3.dtsi 对应 MIPI-CSI_3

**单摄像头配置**

使用 MIPI-CSI_0 接口接入 imx415，仅单个摄像头。

```diff
(SDK)$ vim kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7.dtsi
+#include "rk3576-kickpi-k7-cam0.dtsi"
+//#include "rk3576-kickpi-k7-cam1.dtsi"
+//#include "rk3576-kickpi-k7-cam3.dtsi"
```

**多摄像头配置**

使用 MIPI-CSI_0 、MIPI-CSI_1、MIPI-CSI_3 接口都接入 imx415，共三个摄像头。

```diff
(SDK)$ vim kernel-6.1/arch/arm64/boot/dts/rockchip/rk3576-kickpi-k7.dtsi
+#include "rk3576-kickpi-k7-cam0.dtsi"
+#include "rk3576-kickpi-k7-cam1.dtsi"
+#include "rk3576-kickpi-k7-cam3.dtsi"
```



