# Armbian_SDK编译

## SDK介绍

内核版本：6.1

Debian系统版本：12

Ubuntu系统版本：2404



### 获取SDK源码

* 网盘目录

```

```

>  *这里一般指代日期，下面同，实际名称以网盘为准

1. 拷贝源码压缩包到编译主机目录（若编译主机为虚拟机，不可放在共享目录下编译）

2. MD5校验文件完整性

```
md5sum rk3576-Armbian-*.tar.gz
```

> 判断结果和 rk3576-linux-*.md5sum 内容一致，则文件下载完整

3. 解压源码压缩包

```
$ mkdir rk3576-armbian
$ tar -zxvf md5sum rk3576-linux-*.tar.gz -C rk3576-armbian/
$ cd rk3576-linux/
$ git reset --hard
```



### 全部编译

```
SDK$ ./compile.sh
```

> 运行后，会进入图形化选择编译界面。
>
> 图形化界面配置须知：
>
> * 方向键选择配置，Enter 键确认配置，空格键选中或取消选中。
> * 支持首字母快速检索，可键盘按下首字母，会跳转到对应首字母选项。

1. 选择不修改 Kernel 配置

![image-20250704140210097](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250704140210097.png)

2. 选中显示 CSC / WIP / EOS / TVB 相关配置（目前 K7 在CSC配置中）

![image-20250704140436194](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250704140436194.png)

3. 同意警告声明

![image-20250704140605228](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250704140605228.png)

4. 向下选中 kickpi-k7 配置

![image-20250704140728595](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250704140728595.png)