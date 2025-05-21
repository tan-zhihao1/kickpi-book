# 02-buildroot系统定制



## 开机LOGO

挂载/dev/mmcblk0p1

替换其中的bootlogo.bmp文件



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

buildroot系统不支持使用apt安装工具，需要用SDK配置buildroot实现工具添加裁剪

### 添加或裁剪buildroot工具

执行命令使用图形化界面配置

``` shell
./build.sh buildroot_menuconfig
```

在图形化界面配置后，保存退出

``` 
./build.sh buildroot_saveconfig
```

会自动保存在sun8iw20p1_t113_defconfig中

然后重新编译打包./build.sh && ./build.sh pack 

buildroot里的工具不保证所有都能正常编译，编译失败可尝试自行解决

常见问题：

此方法添加可编译通过的工具大概率成功，但是裁剪可能会不生效

如果遇到裁剪不生效的情况，尝试以下方法解决

``` shell
rm -rf out/t113/evb1_auto/buildroot/buildroot/target -rf
find out/t113/evb1_auto/buildroot/buildroot -name ".stamp_target_installed" |xargs rm -rf
之后重新./build.sh lunch
重新编译打包./build.sh && ./build.sh pack
```



### 添加用户应用或工具

方法一

> 注意：需要完整正常编译过一次SDK

SDK路径中

``` shell
t113-linux/out/t113/evb1_auto/buildroot/buildroot/target
```

此目录为编译完buildroot生成的文件系统根目录，直接将添加所需内容后重新编译打包./build.sh && ./build.sh pack即可。

方法二

SDK路径中

``` shell
t113-linux/buildroot/buildroot-201902/system/skeleton
```

此目录为编译buildroot前的文件系统根目录，直接将添加所需内容后重新编译打包./build.sh && ./build.sh pack

使用方法二如果不生效则使用以下方法尝试解决
``` shell
rm -rf out/t113/evb1_auto/buildroot/buildroot/target -rf
find out/t113/evb1_auto/buildroot/buildroot -name ".stamp_target_installed" |xargs rm -rf
之后重新./build.sh lunch
重新编译打包./build.sh && ./build.sh pack
```





