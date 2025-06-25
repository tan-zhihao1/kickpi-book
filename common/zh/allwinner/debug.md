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

> `u-boot` 常用命令
>
> `getenv` 获取环境变量
>
> `setenv` 打印环境变量
>
> `print` 打印环境变量
>
> `fdt print` 打印相关设备树参数
>
> `fdt set` 设置相关设备树参数
>
> `boot` 启动内核
>
> `reset` 重新启动内核



## Kernel 调试指南

### 打印/设置寄存器

全志平台实现了 sunxi_dump 机制，使用 `/sys/class/sunxi_dump` 打印/设置相关的寄存器

示例：

```
# 打印 0x02001000 到 0x02001200 这段寄存器数据
/sys/class/sunxi_dump$ echo 0x02001000,0x02001200 > dump
/sys/class/sunxi_dump$ cat dump

# 设备 0x02500100 寄存器数据为 0x02000000
/sys/class/sunxi_dump$ echo 0x02500100 0x02000000 > write
```



### 挂载 debugfs



