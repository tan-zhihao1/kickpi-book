# 04-Linux QT环境搭建



## 1. QT运行环境测试

### Ubuntu20.04

Ubuntu20.04系统默认配置好QT5.12.8运行环境

查看QT库：

```shell
$ ls -l /usr/lib/aarch64-none-linux-gnu/libQt*
```

测试demo位于网盘目录：

```
3-SoftwareData软件资料\Qt_demo\mainwindow
```

将demo拷贝进系统任意目录下，进行测试：

```shell
$ sudo chmod +x ./mainwindow
$ sudo ./mainwindow
```



### Debian11

Debian11系统默认配置好QT5.15.2运行环境

查看QT库：

```shell
$ ls -l /usr/lib/aarch64-none-linux-gnu/libQt*
```

测试demo位于网盘目录：

```
3-SoftwareData软件资料\Qt_demo\mainwindow
```

将demo拷贝进系统任意目录下，进行测试：

```shell
$ sudo chmod +x ./mainwindow
$ sudo ./mainwindow
```



## 2. QT交叉编译环境搭建

> 测试平台：虚拟机Ubuntu20.04

### gcc交叉编译工具

**虚拟机：**

可以在网盘中获取，网盘路径：

```
rk356x_data\3-SoftwareData软件资料\gcc
```

SDK自带交叉编译工具链：（不推荐，会出现版本不兼容）

```
(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```

* gcc 交叉编译链安装

将获取的压缩包放到虚拟机目录下进行解压

```shell
$ sudo tar -xvf gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu.tar.xz -C /opt/gcc-arm
```

> -C /opt/gcc-arm 指定解压出的路径

* 配置环境变量

为了让系统能够识别交叉编译工具链，需要将其路径添加到环境变量中。

**临时生效（仅当前终端会话有效）**
运行以下命令：

```shell
$ export PATH=/opt/gcc-arm/bin:$PATH
```

**永久生效**
编辑用户或系统的环境变量配置文件。

```shell
$ sudo vim /etc/profile
export PATH=/opt/gcc-arm/bin:$PATH
```

```shell
source /etc/profile
```

验证安装
运行以下命令验证是否成功安装并配置了工具链：

```shell
aarch64-none-linux-gnu-gcc --version
```

如果输出类似以下内容，则说明安装成功：

```
aarch64-none-linux-gnu-gcc (GNU Toolchain for the A-profile Architecture) 9.2.1 20191203
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

### qmake工具安装

> 如果是想在板子上安装QT，在板子上安装gcc，无需修改编译工具，安装源码即可

​	qmake工具是用于Qt项目根据工程文件.pro生成MakeFile的工具。对于简单的工程，只需要在工程的顶层目录运行qmake。默认情况下，qmake会生成一个构建工程的MakeFile，此时可以运行平台相关的make工具构建工程。

* 网盘只提供一个版本的QT

```
rk356x_data\5-DevelopmentTools开发工具\QT
```

* 官网下载Qt

官网链接：

​	[qt不在维护版本](https://download.qt.io/new_archive/qt/) 

​	[qt在维护版本](https://download.qt.io/official_releases/qt/)

访问官网选择下载需要的版本

![image-20241028093459681](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093459681.png)

选择single

![image-20241028093254591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093254591.png)

选择tar.xz

![image-20241028093434074](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093434074.png)

安装依赖

```shell
$ sudo apt-get install build-essential perl python3 git
$ sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev
$ sudo apt-get install flex bison gperf libicu-dev libxslt-dev ruby
$ sudo apt-get install libxcursor-dev libxcomposite-dev libxdamage-dev libxrandr-dev libxtst-dev libxss-dev libdbus-1-dev libevent-dev libfontconfig1-dev libcap-dev libpulse-dev libudev-dev libpci-dev libnss3-dev libasound2-dev libegl1-mesa-dev gperf bison nodejs
$ sudo apt-get install libasound2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev 
$ sudo apt-get install libgstreamer-plugins-bad1.0-dev
$ sudo apt install clang libclang-dev
$ sudo apt-get install xz-utils
$ sudo ln -s /usr/bin/python3 /usr/bin/python
```

下载后进行解压在opt文件夹中

```shell
$ sudo tar -xvf qt-everywhere-src-5.12.2.tar.xz -C /opt/
```

解压完成后进入文件夹，并建立auto.sh脚本文件。

```shell
$ sudo vim auto.sh
```

```bash
#!/bin/bash

./configure \
    -prefix /opt/Qt/ \
    -opensource -confirm-license \
    -nomake examples \
    -nomake tests \
    -release \
    -xplatform linux-aarch64-gnu-g++ \
    -device-option CROSS_COMPILE=/opt/gcc-arm/bin/aarch64-none-linux-gnu- \
    -no-eglfs \
    -no-xcb \
    -no-opengl \
    -skip qt3d \
    -skip qtcharts \
    -skip qtandroidextras \
    -skip qtlocation \
    -skip qtmultimedia \
    -skip qtsensors \
    -skip qtserialbus \
    -skip qtserialport \
    -skip qtwayland \
    -skip qtdeclarative \
    -skip qtxmlpatterns \
    -skip qtwebchannel \
    -skip qtwebengine \

```

修改qmake.conf文件

QT源码中的qtbase/mkspecs/目录下有各种不同的平台，我们示例中使用的是linux-aarch64-gun-g++目录下的qmake配置文件，但是交叉工具不一样，得修改qmake.conf文件以适配工具

```c++
# modifications to g++.conf
QMAKE_CC                =/opt/gcc-arm/bin/aarch64-none-linux-gnu-gcc
QMAKE_CXX               =/opt/gcc-arm/bin/aarch64-none-linux-gnu-g++
QMAKE_LINK              =/opt/gcc-arm/bin/aarch64-none-linux-gnu-g++
QMAKE_LINK_SHLIB        =/opt/gcc-arm/bin/aarch64-none-linux-gnu-g++
 
# modifications to linux.conf
QMAKE_AR                =/opt/gcc-arm/bin/aarch64-none-linux-gnu-ar cqs
QMAKE_OBJCOPY           =/opt/gcc-arm/bin/aarch64-none-linux-gnu-objcopy
QMAKE_NM                =/opt/gcc-arm/bin/aarch64-none-linux-gnu-nm -P
QMAKE_STRIP             =/opt/gcc-arm/bin/aarch64-none-linux-gnu-strip
```

运行脚本交叉配置QT源码

```shell
$ sudo chmod 755 auto.sh
$ sudo ./auto.sh
```

> 脚本是为了执行./configure
> -prefix /opt/Qt/  \ Qt安装路径
> -opensource -confirm-license \ 安装版本，社区版还是商业版
> -nomake examples \ 不编译安装例子
> -nomake tests \ 不编译安装测试
> -release \ release编译
> -xplatform \指定目标平台
> -skip  \跳过编译模块
>
> qtlocation qtwebengine ，如果不是特别需要这两个模块，建议放弃编译
> 这些命令在解压后的README中有说明
> 执行后根据提示进行改进，大部分提示错误是依赖安装不全

执行编译

```shell
$ sudo make -j4
```

> 时间较长，基于性能4-12小时

完成后执行

```shell
$ sudo make install
```

此命令会将程序安装在上面-prefix指定的/opt/Qt/ 中。
安装成功后修改环境变量：

```shell
$ sudo vim /etc/profile
```

```shell
export QTDIR=/opt/Qt
export PATH=$QTDIR/bin:$PATH
export MANPATH=$QTDIR/man:$MANPATH
export LD_LIBRARY_PATH=$QTDIR/lib:$LD_LIBRARY_PATH
```

```shell
$ source /etc/profile
```

最后执行

```shell
$ qmake -v
```

![image-20241028094050024](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094050024.png)

### 安装qtcreator

方法一：

安装qtcreator必须先安装Qt，用Qt编译qtcreator，如果编译Qt出错，这个位置编译也会出错。如果Qt编译时，有未编译的模块或者含有不依赖的模块，qt编译不受影响，但是qtcreator会有影响。我试过加-no-opengl \，编译qt没有错误，安装也可以，编译qtcreator出错。

* 虚拟机可以选择安装包程序安装，同时可以安装其他在本机运行的包

![image-20250219155414011](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155414011.png)

​	下载后到虚拟机中运行安装程序

```
chmod +x qt-opensource-linux-x64-5.12.2.run
./qt-opensource-linux-x64-5.12.2.run
```

> 这里需要在QT官网注册自己的账号，图像化安装，流程和Windows的一致

方法二：

直接通过apt安装对应系统的qtcreator,但是可能和你安装的QT版本不一致

```
sudo apt update
sudo apt install qtcreator
```

方法三：

**下面这种是特殊情况下需要的编译特定版本的Qtcreator才使用 不推荐**

Qtcreator最好和Qt版本相对应
[Qtcreator源码](https://download.qt.io/archive/qtcreator/)

![image-20241028094210629](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094210629.png)

下载完成后解压
这里默认上面教程以完成好以后：
进入解压路径，执行

```shell
$ qmaek -r
```

等待完成后生成MakeFile文件。
然后执行

```shell
$ sudo make -j4
```

等待编译完成后，执行

```shell
$ sudo make install
```

执行完

打开bin文件夹就会有qtcreator和qtcreator.sh
执行

```shell
$ ./qtcreator.sh &
```

这样qtcreator会在后台运行。
最后配置qtcreator即可。

### QT交叉编译的Kit配置

​	下面介绍QT创建QT项目时的使用交叉编译工具的配置

​	Kits主要是选择gcc、g++为交叉编译工具链中的，qmake使用编译出的qmake

添加GCC中**C、C++** 

![image-20250416112806987](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416112806987.png)

添加qmake

![image-20250416112845224](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416112845224.png)

最终配置

![image-20250416112945015](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416112945015.png)





