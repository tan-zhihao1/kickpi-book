# Android_Logo_Bootanimation



## 开机LOGO

| 主控                 | 型号         | LOGO路径                                                     |
| -------------------- | ------------ | ------------------------------------------------------------ |
| A133                 | K5/K5C       | longan/device/config/chips/a133/configs/c3/android/bootlogo.bmp |
| H618                 | K2B/K2C      | longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp |
| RK3562/RK3568/RK3588 | K1/K1B/K3/K8 | kernel-5.10/logo.bmp<br />kernel-5.10/logo_kernel.bmp        |
| RK3576               | K7/K7C       | kernel-6.1/logo.bmp<br />kernel-6.1/logo_kernel.bmp          |

>rockchip 平台区分 Uboot、Kernel两个阶段显示LOGO，logo.bmp 为Uboot阶段，logo_kernel.bmp为Kernel阶段；



## 开机动画

| 主控                 | 型号         | 开机动画路径                                                 |
| -------------------- | ------------ | ------------------------------------------------------------ |
| A133                 | K5/K5C       | longan/device/config/chips/a133/configs/c3/android/bootlogo.bmp |
| H618                 | K2B/K2C      | longan/device/config/chips/h618/boot-resource/boot-resource/bootlogo.bmp |
| RK3562/RK3568/RK3588 | K1/K1B/K3/K8 | kernel-5.10/logo.bmp kernel-5.10/logo_kernel.bmp             |
| RK3576               | K7/K7C       | device/rockchip/common/bootanimation.zip                     |
