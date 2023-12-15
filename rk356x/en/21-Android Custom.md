# 21-Android Custom



## 1. Customizing Boot LOGO

The boot LOGO consists of two parts: the Uboot stage and the Kernel stage.

For the Uboot stage, analyze the image `kernel-5.10/logo.bmp`.

For the Kernel stage, analyze the image `kernel-5.10/logo_kernel.bmp`.

```shell
$ ls kernel-5.10/
    logo.bmp
    logo_kernel.bmp
```

## 2. Customizing Boot Animation

```shell
$ ls device/rockchip/common/bootanimation.zip
```

## 3. Default Screen Orientation

### Modify Display Orientation

```shell
$ vim device/rockchip/rk356x/BoardConfig.mk

    SF_PRIMARY_DISPLAY_ORIENTATION := 0
```

### Modify Touch Orientation

...

## 4. Default Hide Status Bar

...

## 5. Default Hide Navigation Bar

...

## 6. Pre-installed Third-Party Apps

...

## 7. Auto-Start Apps on Boot

...

## 8. Default Language

```shell
$ vim build/target/product/full_base.mk
    PRODUCT_LOCALES := en_US
```

| Option | Description |
| ------ | ----------- |
| en_US  | English     |
| zh_CN  | Chinese     |
|        |             |

## 9. Default Time Zone

...

## 10. Default Never Sleep

```shell
$ vim device/rockchip/rk356x/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
    <integer name="def_screen_off_timeout">2147483647</integer>
```

## 11. Default Screen Lock Disabled

```shell
$ vim frameworks/base/packages/SettingsProvider/res/values/defaults.xml
    <bool name="def_lockscreen_disabled">true</bool>
```
