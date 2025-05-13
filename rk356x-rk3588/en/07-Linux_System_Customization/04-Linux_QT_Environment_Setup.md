# 04-Linux_QT_Environment_Setup

## 1. QT Runtime Environment Testing

### Ubuntu 20.04
The Ubuntu 20.04 system has the QT 5.12.8 runtime environment pre - configured by default.

To view the QT libraries:
```shell
$ ls -l /usr/lib/aarch64-none-linux-gnu/libQt*
```

The test demo is located in the network drive directory:
```
3-SoftwareData Software Materials\Qt_demo\mainwindow
```

Copy the demo to any directory in the system and conduct the test:
```shell
$ sudo chmod +x ./mainwindow
$ sudo ./mainwindow
```

### Debian 11
The Debian 11 system has the QT 5.15.2 runtime environment pre - configured by default.

To view the QT libraries:
```shell
$ ls -l /usr/lib/aarch64-none-linux-gnu/libQt*
```

The test demo is located in the network drive directory:
```
3-SoftwareData Software Materials\Qt_demo\mainwindow
```

Copy the demo to any directory in the system and conduct the test:
```shell
$ sudo chmod +x ./mainwindow
$ sudo ./mainwindow
```

## 2. QT Cross - Compilation Environment Setup

> Test platform: Virtual machine Ubuntu 20.04

### GCC Cross - Compilation Tools

**Virtual Machine**:
You can obtain it from the network drive. The network drive path is:
```
rk356x_data\3-SoftwareData Software Materials\gcc
```

The SDK comes with a cross - compilation toolchain: (Not recommended as version incompatibilities may occur)
```
(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```

* GCC Cross - Compilation Chain Installation
Place the obtained compressed package in the virtual machine directory and extract it:
```shell
$ sudo tar -xvf gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu.tar.xz -C /opt/gcc-arm
```
> -C /opt/gcc-arm specifies the extraction path.

* Environment Variable Configuration
To enable the system to recognize the cross - compilation toolchain, you need to add its path to the environment variables.

**Temporary Effect (Valid only for the current terminal session)**
Run the following command:
```shell
$ export PATH=/opt/gcc-arm/bin:$PATH
```

**Permanent Effect**
Edit the user or system environment variable configuration file.
```shell
$ sudo vim /etc/profile
export PATH=/opt/gcc-arm/bin:$PATH
```
```shell
source /etc/profile
```

Verify the installation
Run the following command to verify whether the toolchain is successfully installed and configured:
```shell
aarch64-none-linux-gnu-gcc --version
```

If the output is similar to the following, the installation is successful:
```
aarch64-none-linux-gnu-gcc (GNU Toolchain for the A-profile Architecture) 9.2.1 20191203
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

### QMake Tool Installation

> If you want to install QT on the board, install GCC on the board. There is no need to modify the compilation tools. Just install the source code.

The qmake tool is used to generate MakeFiles for Qt projects based on the project file (.pro). For simple projects, you only need to run qmake in the top - level directory of the project. By default, qmake will generate a MakeFile for building the project. At this time, you can run the platform - related make tool to build the project.

* The network drive only provides one version of QT:
```
rk356x_data\5-DevelopmentTools Development Tools\QT
```

* Download Qt from the official website
  Official website links:
  [Unmaintained Qt versions](https://download.qt.io/new_archive/qt/) 
  [Maintained Qt versions](https://download.qt.io/official_releases/qt/)

Visit the official website and select the version you need to download.

![image-20241028093459681](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093459681.png)

Select "single".

![image-20241028093254591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093254591.png)

Select "tar.xz".

![image-20241028093434074](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093434074.png)

Install dependencies:
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

After downloading, extract it in the /opt folder:
```shell
$ sudo tar -xvf qt-everywhere-src-5.12.2.tar.xz -C /opt/
```

After extraction, enter the folder and create an auto.sh script file.
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

Modify the qmake.conf file
There are various platforms in the qtbase/mkspecs/ directory of the QT source code. In our example, we use the qmake configuration file in the linux - aarch64 - gun - g++ directory. However, since the cross - compilation tools are different, we need to modify the qmake.conf file to adapt to the tools.
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

Run the script to cross - configure the QT source code:
```shell
$ sudo chmod 755 auto.sh
$ sudo ./auto.sh
```
> The script is used to execute ./configure
> -prefix /opt/Qt/  \ Qt installation path
> -opensource -confirm-license \ Installation version, community edition or commercial edition
> -nomake examples \ Do not compile and install examples
> -nomake tests \ Do not compile and install tests
> -release \ Release compilation
> -xplatform \ Specify the target platform
> -skip  \ Skip compiling modules
>
> qtlocation and qtwebengine. If you don't particularly need these two modules, it is recommended to skip compiling them.
> These commands are described in the README after extraction.
> After execution, make improvements according to the prompts. Most of the prompt errors are due to incomplete dependency installation.

Execute the compilation:
```shell
$ sudo make -j4
```
> It takes a long time, about 4 - 12 hours depending on the performance.

After completion, execute:
```shell
$ sudo make install
```
This command will install the program in the /opt/Qt/ directory specified by -prefix above.
After successful installation, modify the environment variables:
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

Finally, execute:
```shell
$ qmake -v
```

![image-20241028094050024](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094050024.png)

### Install Qt Creator
You must install Qt first before installing Qt Creator. Compile Qt Creator with Qt. If there is an error in compiling Qt, there will also be an error in this step. If there are uncompiled modules or non - dependent modules during Qt compilation, Qt compilation will not be affected, but Qt Creator will be affected. I tried adding -no - opengl \. There was no error in compiling Qt, and the installation was successful, but there was an error in compiling Qt Creator.

* You can choose to install the package program on the virtual machine. At the same time, you can install other packages that run on the local machine.

![image-20250219155414011](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155414011.png)

Download it and run the installation program in the virtual machine.
It is best to match the Qt Creator version with the Qt version.
[Qt Creator source code](https://download.qt.io/archive/qtcreator/)

![image-20241028094210629](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094210629.png)

After downloading, extract it.
Assuming the above tutorial has been completed:
Enter the extraction path and execute:
```shell
$ qmake -r
```
Wait for the completion to generate the MakeFile file.
Then execute:
```shell
$ sudo make -j4
```
Wait for the compilation to complete, and then execute:
```shell
$ sudo make install
```
After execution,
Open the bin folder, and there will be qtcreator and qtcreator.sh.
Execute:
```shell
$ ./qtcreator.sh &
```
In this way, Qt Creator will run in the background.
Finally, configure Qt Creator.

### QT Cross - Compilation Kit Configuration
The following introduces the configuration of using cross - compilation tools when creating a QT project in QT.
The Kits mainly select GCC and G++ from the cross - compilation toolchain, and use the compiled qmake for qmake.

Add **C and C++** in GCC:
![image-20250416112806987](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416112806987.png)

Add qmake:
![image-20250416112845224](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416112845224.png)

Final configuration:
![image-20250416112945015](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250416112945015.png)