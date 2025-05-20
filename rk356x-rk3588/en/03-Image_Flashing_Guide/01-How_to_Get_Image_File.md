# 01-How_to_Get_Image_File


Image files can be obtained in the following two ways: downloading from the cloud drive or compiling the SDK.

For detailed steps of SDK compilation, refer to the documents [LINUX_SDK Source Code Compilation](../04-SDK Compilation/02-LINUX_SDK Compilation.md) and [ANDROID_SDK Source Code Compilation](../04-SDK Compilation/03-ANDROID_SDK Compilation.md).

## Cloud Drive Directory

```
2-Image/
├── RK3562-K3
│   ├── Android13.0
│   │   ├── back
│   │   │   ├── update-rk3562-kickpi-k3-android-disable-ir-2024053009.img
│   │   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2024051101.img
│   │   │   └── update-rk3562-kickpi-k3-android-test-2024052709.img
│   │   ├── UPDATE_LOG.txt
│   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2025030314.img
│   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2025031919.img
│   │   ├── update-rk3562-kickpi-k3-android-multi-lcd-2025040216.img
│   │   └── New 7-inch Calibration Image
│   │       └── update-rk3562-kickpi-k3-android-multi-lcd-2025042817.img
│   ├── Debian11
│   │   ├── update_log.txt
│   │   ├── update-rk3562-kickpi-k3-linux-debian-multi-lcd--20250304-180123.img
│   │   ├── update-rk3562-kickpi-k3-linux-debian-multi-lcd--20250321-092932.img
│   │   └── update-rk3562-kickpi-k3-linux-debian-multi-lcd--20250407-201723.img
│   └── Ubuntu2004
│       ├── Update Log.txt
│       ├── update-rk3562-kickpi-k3-linux-ubuntu-multi-lcd--20250304-174704.img
│       ├── update-rk3562-kickpi-k3-linux-ubuntu-multi-lcd--20250321-091543.img
│       └── update-rk3562-kickpi-k3-linux-ubuntu-multi-lcd--20250407-151924.img
├── RK3566-TX66
│   ├── Android11-TV
│   │   └── update-rk3566-android11-2023052415.img
│   ├── Android13
│   │   └── update-rk3566-kickpi-tx66-android-2023072110.img
│   ├── Debian11
│   │   └── update-rk3566-box-tx66-debian-2023052415.img
│   └── Ubuntu2004
│       └── update-rk3566-box-tx66-ubuntu-2023052415.img
├── RK3568-K1
│   ├── Android13.0
│   │   ├── UPDATE_LOG.txt
│   │   ├── update-rk3568-kickpi-k1-android-multi-lcd-2025031920.img
│   │   └── update-rk3568-kickpi-k1-android-multi-lcd-2025040218.img
│   ├── Debian11
│   │   ├── update_log.txt
│   │   ├── update-rk3568-kickpi-k1-linux-debian-multi-lcd--20250320-155119.img
│   │   └── update-rk3568-kickpi-k1-linux-debian-multi-lcd--20250403-150845.img
│   └── Ubuntu2004
│       ├── config.cfg
│       ├── Update Log.txt
│       ├── update-rk3568-kickpi-k1-linux-ubuntu-multi-lcd--20250320-140656.img
│       └── update-rk3568-kickpi-k1-linux-ubuntu-multi-lcd---20250403-144048.img
├── RK3568-K1B
│   ├── Android13.0
│   │   ├── UPDATE_LOG.txt
│   │   ├── update-rk3568-kickpi-k1b-android-multi-lcd-2025031914.img
│   │   ├── update-rk3568-kickpi-k1b-android-multi-lcd-2025040314.img
│   │   ├── Firmware with Screen and USB Touch Rotated 90° to the Right
│   │   │   └── update-rk3568-kickpi-k1b-android-2025011009.img
│   │   └── Calibration Firmware for New 7-inch Screen Touch
│   │       ├── README_CN.txt
│   │       ├── README_EN.txt
│   │       └── update-rk3568-kickpi-k1b-android-multi-lcd-2025040918.img
│   ├── Debian11
│   │   ├── update_log.txt
│   │   ├── update-rk3568-kickpi-k1b-linux-debian--multi-lcd--20250320-192313.img
│   │   └── update-rk3568-kickpi-k1b-linux-debian-multi-lcd--20250403-164358.img
│   └── Ubuntu2004
│       ├── Update Log.txt
│       ├── update-rk3568-kickpi-k1b-linux-ubuntu--20250207-141840.img
│       ├── update-rk3568-kickpi-k1b-linux-ubuntu-multi-lcd--20250304-153910.img
│       ├── update-rk3568-kickpi-k1b-linux-ubuntu-multi-lcd--20250320-173529.img
│       └── update-rk3568-kickpi-k1b-linux-ubuntu-multi-lcd--20250403-161034.img
└── RK3588-K8
    ├── Android13.0
    │   ├── update-rk3588-kickpi-k8-android-2025022811.img
    │   └── update-rk3588-kickpi-k8-android-multi-lcd-2025032517.img
    └── Debian11
        └── update-rk3588-kickpi-k8-linux-debian-multi-lcd-20250506.img
```

## Image Name Parsing

```
update-rk3588-kickpi-k8-android-2025022811.img
A(update)-B(rk3588-kickpi-k8)-C(android)-D(multi-lcd)-E(202504171451).img
```

**Part A**

update - Refers to the complete image package.

**Part B**

The main control model and hardware board type part.

rk3588-kickpi-k8 - Represents the main control model rk3588 and the hardware board type k8.

**Part C**

The system name part.

android - Android system image
ubuntu - Ubuntu system image
debian - Debian system image

**Part D**

Other flags in the image name, used to indicate some special differences in the image.

multi-lcd, default is compatible with multiple screen interfaces
hdmi, default display is the HDMI interface
8-MX080B2140, default display is the screen of model MX080B2140

**Part E**

202504171451 - The image update date.