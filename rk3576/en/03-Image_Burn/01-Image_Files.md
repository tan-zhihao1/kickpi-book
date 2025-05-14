# 01-Obtain Image Files

Image files can be obtained in the following two ways: downloading from the cloud disk and compiling the SDK.

For the detailed steps of SDK compilation, refer to the documents [LINUX_SDK Source Code Compilation](../04-SDK Compilation/02-LINUX_SDK Compilation.md) and [ANDROID_SDK Source Code Compilation](../04-SDK Compilation/03-ANDROID_SDK Compilation.md).

## Cloud Disk Directory

```
2-Image/
├── Android14
│   ├── boot
│   │   └── 20250414
│   │       ├── boot-10.1-MX101BA1340.img
│   │       ├── boot-5-F050008M01.img
│   │       ├── boot-8-MX080B2140.img
│   │       └── parameter.txt
│   ├── update-rk3576-kickpi-k7-android-10.1-AT101DS40I-202504171511.img
│   ├── update-rk3576-kickpi-k7-android-10.1-MX101BA1340-202504171555.img
│   ├── update-rk3576-kickpi-k7-android-5-F050008M01-202504171536.img
│   ├── update-rk3576-kickpi-k7-android-8-MX080B2140-202504171436.img
│   └── update-rk3576-kickpi-k7-android-HDMI-202504171451.img
├── Debian12
│   ├── update-rk3576-kickpi-k7-linux-debian-10.1-AT101DS40I-20250417-110424.img
│   ├── update-rk3576-kickpi-k7-linux-debian-10.1-MX101BA1340-20250417-114626.img
│   ├── update-rk3576-kickpi-k7-linux-debian-5-F050008M01-20250417-111519.img
│   ├── update-rk3576-kickpi-k7-linux-debian-8-MX080B2140-20250417-113537.img
│   └── update-rk3576-kickpi-k7-linux-debian-HDMI-20250417-115658.img
├── Ubuntu2404
│   ├── update-rk3576-kickpi-k7-linux-ubuntu-10.1-AT101DS40I20250417-104317.img
│   ├── update-rk3576-kickpi-k7-linux-ubuntu-10.1-MX101BA1340-20250417-093953.img
│   ├── update-rk3576-kickpi-k7-linux-ubuntu-5-F050008M01-20250417-102300.img
│   ├── update-rk3576-kickpi-k7-linux-ubuntu-8-MX080B2140-20250417-100614.img
│   └── update-rk3576-kickpi-k7-linux-ubuntu-HDMI-20250417-092759.img
└── UPDATE_LOG.txt
```

## Image Name Parsing

```
update-rk3576-kickpi-k7-android-HDMI-202504171451.img
A(update)-B(rk3576-kickpi-k7)-C(android)-D(HDMI)-E(202504171451).img
```

### Part A
update - Refers to the complete image package.

### Part B
This part represents the main control model and hardware board type.
rk3576-kickpi-k7 - Represents the main control model rk3576 and the hardware board type k7.

### Part C
This part represents the system name.
android - Android system image
ubuntu - Ubuntu system image
debian - Debian system image

### Part D
Other identifiers in the image name, used to indicate some special differences of the image.
hdmi - The default display is through the HDMI interface.
8-MX080B2140 - The default display is the screen of model MX080B2140.

### Part E
202504171451 - The update date of the image.