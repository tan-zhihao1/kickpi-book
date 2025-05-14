# Windows_QT开发环境搭建

Windows10/11下交叉编译aarch64(ARMv8)架构Qt5.12.10

下载QT开发环境相关软件包，网盘这个目录下的都要下载

h618_data\3-SoftwareData\Qt_environment_installation

## 安装QT

打开qt安装程序

![image-20250305160406754](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305160406754.png)

建议断网安装，否则需要登录

![image-20250305154700903](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305154700903.png)

![image-20250305154733307](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305154733307.png)

如下图选择工具

![image-20250305184749897](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305184749897.png)

同意相关条款

![image-20250305154947584](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305154947584.png)

![image-20250305155351891](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305155351891.png)

![image-20250305155413384](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305155413384.png)

![image-20250305155506960](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305155506960.png)

在安装目录下Qt5.12.10\Tools\mingw730_64\bin里复制一份mingw32-make.exe并重命名为make.exe

![image-20250306134109785](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306134109785.png)

## 配置环境变量

![image-20250305155737717](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305155737717.png)

![image-20250305155931861](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305155931861.png)

![image-20250305192050626](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305192050626.png)

## 安装关联

打开

![image-20250305160346146](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305160346146.png)

![image-20250305160301502](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305160301502.png)

完成后打开系统powershell执行以下

``` cmd
state checkout ActiveState-Projects/ActiveState-Perl-5.36.0 .
```

![image-20250305160827905](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305160827905.png)

![image-20250305160921652](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305160921652.png)

``` cmd
state use ActiveState-Perl-5.36.0
```

![image-20250305161126514](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305161126514.png)

设置环境变量

![image-20250305192134709](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305192134709.png)

## 安装python

![image-20250305161451095](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305161451095.png)

![image-20250305161534791](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305161534791.png)

![image-20250305161614854](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305161614854.png)

根据安装目录添加环境变量

![image-20250305192154980](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305192154980.png)

检查版本

![image-20250305162520633](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305162520633.png)

解压编译工具链并添加环境变量

![image-20250305162811689](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305162811689.png)

解压过后的名字比较长，编译可能出问题，将这个文件夹名字改成aarch64-linux-gnu

![image-20250305192216104](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305192216104.png)

## 编译QT源码

解压Qt源码

![image-20250305192953074](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305192953074.png)

1. 在Windows搜索MinGW，打开如下图所示的窗口（注意看版本，别选错了），*\*注意，一定要用这个工具打开，不能用PowerShell\**

![image-20250305185044779](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305185044779.png)

![image-20250305191930110](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305191930110.png)

切换到Qt源码目录下

如果源码和5.12.10(MinGW 7.3.0 64-bit)运行时不在同一盘符下

则如图D:切换下，

![image-20250305192832092](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305192832092.png)

执行下面的语句

``` shell
.\configure.bat -release -opensource -prefix D:\Qt\CrossCompilation\Qt5.12.10-ARMv8 -nomake tests -nomake examples -no-opengl -skip qtvirtualkeyboard -platform win32-g++ -xplatform linux-aarch64-gnu-g++

-prefix 后面的参数为安装路径
```

![image-20250305193934069](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305193934069.png)

接受开源协议

![image-20250305194131228](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305194131228.png)

根据命令行提示，输入y完成配置

根据硬件情况选择编译的核心数，比如8核心12线程（预计一小时左右），则建议

``` cmd
mingw32-make -j10
```

![image-20250305194249489](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250305194249489.png)

尽量比线程数少1-2，否则电脑可能过于卡顿，编译时间较长，核心数越多编译越快，只要编译过程没有跳出error并中断编译就说明编译通过

![image-20250306091059865](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306091059865.png)

安装大概10分钟

``` 
mingw32-make install
```

![image-20250306091941590](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306091941590.png)

## 配置QT Creator

![image-20250306092046919](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306092046919.png)

### 配置QT Version

1. 打开QT Creator，打开“工具->选项->Kits”
2. 在“QT Version”栏点击添加，选择上方QT安装目录下的bin目录下的qmake.exe，点击右下角apply

![image-20250306092221235](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306092221235.png)

![image-20250306092448631](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306092448631.png)

### 配置编译器Compilers

选择“添加->GCC->C++”，编译器路径即为gnu工具中的C++编译器aarch64-linux-gnu-g++所在路径，名称建议与我截图aarch64-linux-gnu-g++保持一致，ABI使用默认即可

选择“添加->GCC->C”，编译器路径即为gnu工具中的C编译器aarch64-linux-gnu-gcc所在路径，名称建议与我截图aarch64-linux-gnu-gcc保持一致，ABI使用默认即可

![image-20250306093823225](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306093823225.png)

![image-20250306093727297](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306093727297.png)

与我截图保持一致，点击右下角apply

### 配置Debuggers

在“Debuggers”点击右边Add添加，选择gnu工具下的aarch64-linux\bin路径下的aarch64-linux-gnu-gdb.exe，Name修改为aarch64-linux-gnu-gdb，点击右下角apply

![image-20250306094016745](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306094016745.png)

### 配置CMake

在“CMake”栏点击Add添加，选择安装的QT目录，找到之前重命名的Tools\mingw730_64\bin\make.exe，参考下图，修改Name为MinGW，点击右下角apply

![image-20250306135029163](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306135029163.png)

### 配置Kits

在“Kits”栏点击添加，所有需要操作的部分参考下图红圈部分

![image-20250306094936335](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306094936335.png)

## 案例测试

### 新建工程

![image-20250306095105641](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095105641.png)

![image-20250306095230390](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095230390.png)

修改工程名和路径

![image-20250306095351963](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095351963.png)

![image-20250306095417246](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095417246.png)

![image-20250306095449959](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095449959.png)

![image-20250306095538732](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095538732.png)

如图D:\Qt\CrossCompilation\Qt_demo\build-Kickpi_qt_demo-ARM_Linux-Release即为编译是生成可执行文件的目录

![image-20250306095718648](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095718648.png)

![image-20250306095752717](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095752717.png)

### 编译

![image-20250306095859812](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306095859812.png)

编译通过

![image-20250306135646187](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306135646187.png)

### 运行

将编译生成的bin文件推送到板卡运行

![image-20250306135749415](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250306135749415.png)

![5bd2329f002770c72243028072b4b9a5](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/5bd2329f002770c72243028072b4b9a5.jpg)
