# Linux_QT开发环境搭建

Qt是一个跨平台的C++应用程序开发框架，广泛应用于桌面、嵌入式和移动设备。在Linux系统下搭建Qt开发环境是一个相对简单的过程，遵循一系列步骤来确保所有依赖项正确安装。

## 准备

- 操作系统：Linux发行版（如Ubuntu）。

- 软件包管理器：例如apt、yum等。

## 安装Qt

1.进入Ubuntu Terminal，更新软件包列表。

```
sudo apt update
sudo apt upgrade
```

2.以Ubuntu为例，可以通过以下命令安装Qt 5核心开发库。

```
sudo apt install -y qtbase5-dev qt5-qmake qtbase5-dev-tools
```

3.验证安装，打开终端并运行以下命令，确认Qt已正确安装。

```
qmake --version
//Qt版本
QMake version 3.1
Using Qt version 5.15.3 in /usr/lib/x86_64-linux-gnu
```

4.（可选）安装常用 Qt5 模块，如果开发中需要特定功能（如网络、数据库、界面组件等），补充安装对应模块。

```
# 示例：安装网络、SQL、图形界面扩展模块
sudo apt install -y libqt5network5-dev libqt5sql5-dev libqt5widgets5-dev
```

## 安装QT Creator

Qt Creator是Qt的开发IDE，它提供了一个直观的用户界面，用于创建和管理Qt项目。

1.安装Qt Creator。

```
sudo apt install qtcreator
```

2.打开终端，输入以下命令启动Qt Creator。

```
qtcreator
```

<img src="http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/QT界面.png" style="zoom:67%;" />

## 配置开发环境

1.**创建新项目**： 打开Qt Creator，点击“File” > “New File or Project” > “Qt Widgets Application”。 按照提示输入项目名称和保存路径。

<img src="http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/QT 创建项目.gif" style="zoom:67%;" />

2.**配置项目**： 在“Projects”菜单中，可以配置编译器、构建目录和调试器等。

<img src="http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20251105153503971.png" alt="image-20251105153503971" style="zoom:67%;" />

