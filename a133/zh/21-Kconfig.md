# 21-Kconfig

内核路径 `longan/kernel/linux-4.9`

编译config配置

```
cd longan/kernel/linux-4.9
make ARCH=arm64 menuconfig
make ARCH=arm64 savedefconfig
diff defconfig arch/arm64/configs/sun50iw10p1smp_a133_android_defconfig
cp defconfig arch/arm64/configs/sun50iw10p1smp_a133_android_defconfig
```

