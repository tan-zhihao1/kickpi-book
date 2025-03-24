# 33-Linux QT环境搭建



## 1. QT运行环境测试

### Ubuntu20.04

Ubuntu20.04系统默认配置好QT5.12.8运行环境

查看QT库：

```shell
$ ls -l /usr/lib/aarch64-linux-gnu/libQt*
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
$ ls -l /usr/lib/aarch64-linux-gnu/libQt*
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



## 2. QT应用编译环境搭建

​	下面介绍虚拟机下安装QT和板子下安装QT的过程

### qmake工具安装

​	qmake工具是用于Qt项目根据工程文件.pro生成MakeFile的工具。对于简单的工程，只需要在工程的顶层目录运行qmake。默认情况下，qmake会生成一个构建工程的MakeFile，此时可以运行平台相关的make工具构建工程。



1.查看当前Linux系统架构 

```
$ uname -m 
```

> 板子系统是ARM架构 虚拟机则是X86_64架构



2.安装依赖

```
$ sudo apt-get install build-essential perl python3 git
$ sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev
$ sudo apt-get install flex bison gperf libicu-dev libxslt-dev ruby
$ sudo apt-get install libxcursor-dev libxcomposite-dev libxdamage-dev libxrandr-dev libxtst-dev libxss-dev libdbus-1-dev libevent-dev libfontconfig1-dev libcap-dev libpulse-dev libudev-dev libpci-dev libnss3-dev libasound2-dev libegl1-mesa-dev gperf bison nodejs
$ sudo apt-get install libasound2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev 
$ sudo apt-get install libgstreamer-plugins-bad1.0-dev
$ sudo apt install clang libclang-dev
$ sudo apt-get install xz-utils
```

3.官网下载安装Qt

官网链接：

​	[qt不在维护版本](https://download.qt.io/new_archive/qt/) 

​	[qt在维护版本](https://download.qt.io/official_releases/qt/)

访问官网选择下载需要的版本

![image-20241028093459681](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093459681.png)

虚拟机X86_64架构选择：

![image-20250219155414011](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155414011.png)

​	下载后到虚拟机中运行安装程序

板子ARM架构选择：

选择single

![image-20241028093254591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093254591.png)

选择tar.xz

![image-20241028093434074](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093434074.png)

下载后进行解压在opt文件夹中

```
$ sudo tar -xvf qt-everywhere-src-5.14.2.tar.xz -C /opt/
```

解压完成后进入文件夹，并建立auto.sh脚本文件。

```
$ sudo vim auto.sh
```

```
#! /bin/bash
./configure -prefix /opt/Qt/ \
-opensource -confirm-license \
-nomake examples \
-nomake tests \
-release \
-skip qt3d \
-skip qtcharts \
-skip qtandroidextras \
-skip qtlocation \
-skip qtmultimedia \
-skip qtsensors \
-skip qtserialbus \
-skip qtserialport \
-skip qtwayland \
-skip qtwebengine \

```

```
$ sudo chmod 755 auto.sh
$ sudo ./auto.sh
```

> 脚本是为了执行./configure
> -prefix /opt/Qt/ \ Qt安装路径
> -opensource -confirm-license \ 安装版本，社区版还是商业版
> -nomake examples \ 不编译安装例子
> -nomake tests \ 不编译安装测试
> -release \ release编译
> -skip qt3d \ 跳过编译模块
> -skip qtcharts \ 跳过编译模块
> -skip qtandroidextras \ 跳过编译模块
> -skip qtlocation \ 跳过编译模块，此模块容易出错
> -skip qtmultimedia \ 跳过编译模块
> -skip qtsensors \ 跳过编译模块
> -skip qtserialbus \ 跳过编译模块
> -skip qtserialport \ 跳过编译模块
> -skip qtwayland \ 跳过编译模块
> -skip qtwebengine \ 跳过编译模块，此模块容易出错
>
> qtlocation qtwebengine ，如果不是特别需要这两个模块，建议放弃编译
> 这些命令在解压后的README中有说明
> 执行后根据提示进行改进，大部分提示错误是依赖安装不全

执行编译

```
$ sudo make -j4
```

> 时间较长，基于板子功能4-12小时

##### FAQ ：如果已经安装python3，但是构建过程中提示缺少了 Python 解释器。可以创建一个符号链接：

```
$ sudo ln -s /usr/bin/python3 /usr/bin/python
```

完成后执行

```
$ sudo make install
```

此命令会将程序安装在/opt/Qt/ 中。
安装成功后修改环境变量：

```
$ sudo vim /etc/profile
```

```
export QTDIR=/opt/Qt
export PATH=$QTDIR/bin:$PATH
export MANPATH=$QTDIR/man:$MANPATH
export LD_LIBRARY_PATH=$QTDIR/lib:$LD_LIBRARY_PATH
```

```
$ source /etc/profile/
```

最后执行`qmake -v`

![image-20241028094050024](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094050024.png)

### 编译安装qtcreator

> 虚拟机安装程序一般会同步安装qtcreator 无需再安装 

安装qtcreator必须先安装Qt，用Qt编译qtcreator，如果编译Qt出错，这个位置编译也会出错。如果Qt编译时，有未编译的模块或者含有不依赖的模块，qt编译不受影响，但是qtcreator会有影响。我试过加-no-opengl \，编译qt没有错误，安装也可以，编译qtcreator出错。

Qtcreator最好和Qt版本相对应
[Qtcreator源码](https://download.qt.io/archive/qtcreator/)

![image-20241028094210629](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094210629.png)

下载完成后解压
这里默认上面教程以完成好以后：
进入解压路径，执行

```
$ qmaek -r
```

等待完成后生成MakeFile文件。
然后执行

```
$ sudo make -j4
```

等待编译完成后，执行

```
$ sudo make install
```

执行完

打开bin文件夹就会有qtcreator和qtcreator.sh
执行`./qtcreator.sh &`
这样qtcreator会在后台运行。
最后配置qtcreator即可。
工具—>选项—>Kits

Kits选择gcc为板子对应的编译工具链即可，一般qtcreator默认设配好

### gcc交叉编译工具

虚拟机：

可以在网盘中获取，网盘路径：

```
rk356x_data\3-SoftwareData软件资料\gcc
```

SDK自带交叉编译工具链：

```
(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```

板子端：无需交叉编译











