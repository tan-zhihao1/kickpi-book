# Android_Screen_Rotation

## A133 源码修改屏幕方向

第一步，修改logo显示图片方向

```
longan/device/config/chips/a133/configs/c3/android/bootlogo.bmp
```

第二步，修改待机充电画面显示方向

![image-20250709141501180](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250709141501180.png)

第三步，修改Android系统默认显示方向

![image-20250606165333617](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250606165333617.png)

第四步，修改触摸方向。

10寸 MIPI V2 屏触控修改参考如下，注意选择对应屏幕的设备树文件。

![image-20250606165428265](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250606165428265.png)



### 命令修改屏幕方向

支持使用adb命令进行旋转，能够单次旋转屏幕，重启后失效。

```
// 0
adb shell settings put system user_rotation 0

// 90°
adb shell settings put system user_rotation 1

// 180°
adb shell settings put system user_rotation 2

// 270°
adb shell settings put system user_rotation 3
```

