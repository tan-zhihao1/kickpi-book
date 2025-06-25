# Debug



## U-boot 调试指南

**boot 命令行配置**

开启 boot 命令行调试，修改 env.cfg 文件中的 bootdelay 参数大于 0 。

```
$ vim device/config/chip/{IC}/configs/default/env.cfg 
-bootdelay=0
+bootdelay=1
```



**进入 boot 命令行**

在系统启动过程中，按下 ssss 即可进入boot 命令行。

双击 Tab 键获取 boot 支持的命令。



