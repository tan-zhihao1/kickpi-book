# 开机自启动





## 关闭开机自启动QT程序

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

## 添加开机自启动程序

板卡系统上

参考这里的文件编辑一份脚本

``` shell
etc/init.d/
```

SDK里的路径为

``` shell
buildroot/config/buildroot/allwinner/system/busybox-init-base-files/etc/init.d/
```





