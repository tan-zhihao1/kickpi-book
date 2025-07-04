# Armbian常用系统定制



## 符号说明

* `SDK$`：指代源码路径

* `console$`：泛指主板的命令行控制台。[主板命令行控制台](../02-入门必读/02-快速使用.md#console_readme)

* `ADB$`：Android Debug Bridge 命令行工具，泛指可运行 ADB 的环境



## Chromium 浏览器

> 推荐使用 chromium 浏览器，目前测试播放视频较为流畅。

安装方式

```
console$ sudo apt update
console$ sudo apt install chromium
```



## FireFox 浏览器

> 不推荐使用 firefox 浏览器，目前会存在 profile 问题，导致重启后无法打开浏览器。

**安装方式**

```
console$ sudo apt update
console$ sudo apt install chromium
```

**解决方法一**

在linux的终端输入

```
$ firefox -profilemanager
```

> 删除错误的 profile 
>
> 新建profile 来启动 firefox。



**解决方法二**

```
$ rm ~/.mozilla/firefox/ -rfv
```

