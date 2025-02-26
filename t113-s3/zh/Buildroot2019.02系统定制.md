# Buildroot系统定制

## 开机自启动

### 关闭开机自启动QT程序

删除S90deviceTest_QT或重命名为非‘S’开头

板卡系统上

``` shell
etc/init.d/S90deviceTest_QT
mv etc/init.d/S90deviceTest_QT etc/init.d/D90deviceTest_QT 
```

SDK里的路径为

``` shell
buildroot/config/buildroot/allwinner/system/busybox-init-base-files/etc/init.d/S90deviceTest_QT
```

### 添加开机自启动程序

板卡系统上

参考这里的文件编辑一份脚本

``` shell
etc/init.d/
```

SDK里的路径为

``` shell
buildroot/config/buildroot/allwinner/system/busybox-init-base-files/etc/init.d/
```



## 添加应用

使用的defconfig文件为

buildroot/buildroot-201902/configs/sun8iw20p1_t113_defconfig

### 添加或裁剪buildroot工具

``` shell
./build.sh buildroot_menuconfig
```

在图像化界面配置后，保存退出

``` 
./build.sh buildroot_saveconfig
```



添加工具

裁剪可能会不生效

可能编译不生效

buildroot里的工具不保证所有都能正常编译，编译失败可自行解决



### 添加用户应用或工具

方法一

> 注意：需要完整正常编译过一次SDK

SDK路径中

``` shell
t113-linux/out/t113/evb1_auto/buildroot/buildroot/target
```

此目录buildroot文件系统根目录，直接将shuoy







t113-linux/buildroot/buildroot-201902/system/skeleton

