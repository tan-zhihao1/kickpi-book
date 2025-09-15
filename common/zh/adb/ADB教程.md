# ADB教程

ADB（Android Debug Bridge）是安卓开发调试桥接工具，支持通过 USB 或网络连接设备，可实现应用安装、文件传输、命令执行等设备管理与调试操作。



## ADB快速使用

### 调试线与接口

调试线：USB Type-C 数据线。

调试接口：主板的 USB Type-C 接口。



### **ADB 工具获取与安装**

**工具获取**

ADB软件工具从KICKPI网盘中获取，Windows x64平台使用，位于网盘目录

```
5-DevelopmentTool/win_x64_ADB_Tool/adb-tools.rar
```

**安装步骤**

1. 解压 `adb-tools.rar` 压缩包，如解压到 `D:\ADB Tool\adb-tools` 路径。
2. 打开命令行窗口，切换到解压目录：

```
ADB$ cd D:\ADB Tool\adb-tools
```

3. 运行命令，运行成功代表安装成功

```
ADB$ adb --version
```

示例：

```
PS D:\ADB Tool\adb-tools> .\adb.exe --version
Android Debug Bridge version 1.0.40
Version 4797878
Installed as D:\ADB Tool\adb-tools\adb.exe
```



### **ADB 检测设备**

1. 使用 USB Type-C 数据线将电脑和主板连接起来。
2. 打开命令行窗口，切换到 ADB 工具解压目录，运行以下命令，若接入成功，可检测到对应 device：

```
ADB$ adb devices
```

示例：

```
PS D:\ADB Tool\adb-tools> .\adb.exe devices
List of devices attached
5d5543a9bcc93eec        device
```



## ADB功能使用

下文是一些常用 ADB 命令的介绍。



### 设备连接与管理

**查看已连接设备**

```bash
adb devices
```

该命令用于查看当前通过 ADB 连接到计算机的所有设备。若设备已正确连接且授权，会显示设备的序列号和状态（如“device”）。

**连接网络ADB**

```
adb connect <IP>
```

> 连接网络ADB端口

**重启设备**

```bash
adb reboot
```

此命令能让连接的设备重启。

**adb登录**

``` 
adb -s <序列号> shell
```

> 序列号通过 adb devices 查看，只有一台设备时，可以省略-s 操作

![image-20250509185455550](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509185455550.png)

### 文件操作

**上传文件到设备**

```bash
adb push <本地文件路径> <设备目标路径>
```

例如，要将本地的 `test.txt` 文件上传到设备的 `/sdcard/` 目录下，可使用命令：

```bash
adb push test.txt /sdcard/
```

**从设备下载文件**

```bash
adb pull <设备文件路径> <本地目标路径>
```

若要从设备的 `/sdcard/log.txt` 下载到本地当前目录，可使用：

```bash
adb pull /sdcard/log.txt .
```

**在设备上执行文件系统操作**

```bash
adb shell ls /sdcard
```

此命令可在设备的 shell 环境下执行 `ls` 命令，查看 `/sdcard` 目录下的文件和文件夹列表。



### 应用管理

> 仅 Android 平台支持

**安装应用**

```bash
adb install <APK FilePath>
```

如安装本地的 `app.apk` 文件，使用：

```bash
adb install app.apk
```

**覆盖已安装的应用**

```
adb install -r app.apk
```

**卸载应用**

```bash
adb uninstall <APK Packname>
```

例如卸载包名为 `com.example.app` 的应用，可使用：

```bash
adb uninstall com.example.app
```

**清除应用数据**

```bash
adb shell pm clear <APK Packname>
```

该命令会清除指定应用的所有数据，如清除 `com.example.app` 的数据：

```bash
adb shell pm clear com.example.app
```



### 系统调试

**进入设备的 shell 环境**

```bash
adb shell
```

执行该命令后，会进入设备的命令行界面，可在其中执行各种系统命令。

**查看系统日志**

> 仅 Android 平台支持

```bash
adb logcat
```

此命令用于查看设备的系统日志，可帮助开发者定位应用或系统的问题。按 `Ctrl + C` 可停止日志输出。

**屏幕截图**

> 仅 Android 平台支持

```bash
adb shell screencap -p /sdcard/screenshot.png
```

该命令会在设备的 `/sdcard` 目录下生成一个名为 `screenshot.png` 的屏幕截图文件，之后可使用 `adb pull` 命令将其下载到本地。

## 常见问题

### ADB设备无法识别

进行ADB调试时，等板卡蓝灯闪烁时查看系统设备管理器是否识到是类似如下设备，例如这样：

![image-20250509185304921](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250509185304921.png)

如果识别到的是未知设备，如图所示则需手动安装驱动

![image-20250226152046272](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152046272.png)

手动安装驱动

![370849870429aae4906079eb686ee326](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/370849870429aae4906079eb686ee326.jpg)

![image-20250226152541205](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152541205.png)

![image-20250226152555933](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152555933.png)

![image-20250226152618854](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152618854.png)

![image-20250226152635425](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152635425.png)

![image-20250226152643858](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152643858.png)

![image-20250226152654260](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250226152654260.png)
