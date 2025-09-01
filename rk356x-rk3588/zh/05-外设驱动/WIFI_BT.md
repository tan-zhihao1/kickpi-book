# WIFI_BT

## 文档参考

Android_SDK

```
RKDocs/android/wifi/Rockchip_Introduction_WIFI_Configuration_CN&EN.pdf
RKDocs/android/wifi/Rockchip_Introduction_REALTEK_WIFI_Driver_Porting_CN&EN.pdf
```

Linux_SDK

```
docs/cn/Linux/Wifibt
docs/en/Linux/Wifibt
```



## WiFi 模块

| 型号      | 接口 | **频段支持**          | 无线标准                           |
| --------- | ---- | --------------------- | ---------------------------------- |
| RTL8822CS | SDIO | 2.4GHz 和 5GHz 双频段 | 支持 Wi-Fi 5（802.11ac）和蓝牙 5.0 |
| RTL8852BE | PCIE | 2.4GHz 和 5GHz 双频段 | 支持 Wi-Fi 6（802.11ax）和蓝牙 5.2 |



## DTS

Android_SDK

```
kernel-5.10/arch/arm64/boot/dts/rockchip/rk3568-kickpi-wifibt.dtsi
kernel-5.10/arch/arm64/boot/dts/rockchip/rk3562-kickpi-wifibt.dtsi
kernel-5.10/arch/arm64/boot/dts/rockchip/rk3588-kickpi-wifi-rtl8822be.dtsi
```

Linux_SDK

```
kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-wifibt.dtsi
kernel/arch/arm64/boot/dts/rockchip/rk3562-kickpi-wifibt.dtsi
kernel/arch/arm64/boot/dts/rockchip/rk3588-kickpi-wifi-rtl8822be.dtsi
```



## Driver

RTL8822CS

```
Android_SDK/external/wifi_driver/rtl8822cs/
Linux_SDK/external/rkwifibt/drivers/rtl8822cs/ 
```

RTL8852BE

```
Android_SDK/external/wifi_driver/rtl8852be
Linux_SDK/external/rkwifibt/drivers/rtl8852be
```



## Android

```
packages/apps/Settings/src/com/android/settings/wifi/
frameworks/opt/net/wifi/libwifi_hal/
external/wpa_supplicant_8/
hardware/interfaces/wifi/
hardware/realtek/
```



