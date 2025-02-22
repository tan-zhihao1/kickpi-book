# 33-Linux QT Environment



## 1.QT running environment test

### Ubuntu 20.04

The Ubuntu 20.04 system is configured with the QT running environment by default, and the following commands can be executed to test



### Debian11

The Debian11 system is configured with the QT running environment by default, and the following commands can be executed to test

```
$ cd /usr/lib/aarch64-linux-gnu/qt5/examples/qpa/windows/
$ ./windows
```



* Open the dual QT window interface

![a29f89e0967515369b000d08b2ff832](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/a29f89e0967515369b000d08b2ff832.jpg)





## 2. QT application compilation environment construction

### QT Creator Environment Configuration

Qmake tool

1. View the current Linux system architecture 

```
uname -m 
```

>The board subsystem is an ARM architecture, and the virtual machine is a X86_64 architecture



2. Install dependencies

```
sudo apt-get install build-essential perl python3 git
sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev
sudo apt-get install flex bison gperf libicu-dev libxslt-dev ruby
sudo apt-get install libxcursor-dev libxcomposite-dev libxdamage-dev libxrandr-dev libxtst-dev libxss-dev libdbus-1-dev libevent-dev libfontconfig1-dev libcap-dev libpulse-dev libudev-dev libpci-dev libnss3-dev libasound2-dev libegl1-mesa-dev gperf bison nodejs
sudo apt-get install libasound2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-good1.0-dev libgstreamer-plugins-bad1.0-dev
sudo apt install clang libclang-dev
```

[Qt is not in the maintenance version](https://download.qt.io/new_archive/qt/)

[Qt is in maintenance version](https://download.qt.io/official_releases/qt/)

Select the version you require

![image-20241028093459681](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093459681.png)

Virtual machine selection:

![image-20250219155414011](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250219155414011.png)

​	After downloading, run the installer in the virtual machine

ARM architecture selection:

Select single

![image-20241028093254591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093254591.png)

Select tar.xz

![image-20241028093434074](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093434074.png)

Unzip it after downloading in the opt folder

```
sudo tar -xvf qt-everywhere-src-5.14.2.tar.xz -C /opt/
```

After the decompression is complete, enter the folder and create auto.sh script file.

```
sudo vim auto.sh
sudo chmod 755 auto.sh
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
sudo ./auto.sh
```

The script is for executing./configure
-Prefix /opt/Qt/\ Qt installation path
-OpenSource-confirm-license\ Installed version, community version or commercial version
-Nomake examples\ do not compile installation examples
-Nomake tests\ do not compile installation tests
-Release\ release compilation
-Skip qt3d\ Skip compilation module
-Skip qtcharts\ skip compilation module
-Skip qtandroidextras\ Skip compilation module
-Skip qtlocation\ Skip the compilation module, which is error-prone
-Skip qtmultimedia\ Skip compilation module
-Skip qtsensors\ Skip compilation module
-Skip qtserialbus\ Skip compilation module
-Skip qtserialport\ Skip compilation module
-Skip qtwayland\ Skip compilation module
-Skip qtwebengine\ Skip the compilation module, which is prone to errors

qtlocation qtwebengine, if these two modules are not particularly needed, it is recommended to give up the compilation
These commands are described in the decompressed README
After execution, improve according to the prompts. Most of the prompt errors depend on incomplete installation.
execute

```
sudo make -j4
```

> Long time, 4-12 hours based on board function

##### FAQ: If you have already installed python3, but the Python interpreter is missing during the build process. You can create a symbolic link:

```
sudo ln -s /usr/bin/python3 /usr/bin/python
```

Execute after completion

```
sudo make install
```

This command installs the program in/opt/Qt/.
Modify environment variables after successful installation:

```
sudo vim /etc/profile
```

```
export QTDIR=/opt/Qt
export PATH=$QTDIR/bin:$PATH
export MANPATH=$QTDIR/man:$MANPATH
export LD_LIBRARY_PATH=$QTDIR/lib:$LD_LIBRARY_PATH
```

```
source /etc/profile/
```

Finally execute 'qmake -v'

![image-20241028094050024](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094050024.png)

### Compile and install qtcreator

> The virtual machine installer will generally install qtcreator synchronously, no need to install it again

To install qtcreator, you must first install Qt and compile qtcreator with Qt. If there is an error in compiling Qt, the compilation at this location will also error. If Qt compiles with uncompiled modules or contains non-dependent modules, the qt compilation will not be affected, but the qtcreator will be affected. I tried adding -no-opengl\, and there is no error in compiling qt. You can install it, but the qtcreator compiles with an error.

Qtcreator should preferably correspond to the Qt version
[QtCreator source code](https://download.qt.io/archive/qtcreator/)

![image-20241028094210629](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094210629.png)

Unzip after download is complete
Here, the above tutorial is defaulted to after completion:
Enter the decompression path and execute

```
qmaek -r
```

Wait for the MakeFile file to be generated after completion.
Then execute

```
sudo make -j4
```

After the compilation is completed, execute

```
sudo make install
```

executed

Open the bin folder and there will be qtcreator and qtcreator.sh
Execute './qtcreator.sh &'
This way qtcreator will run in the background.
Finally, you can configure qtcreator.
Tools - > Options - > Kits

Kits can select gcc as the compiler toolchain corresponding to the board. Generally, qtcreator is set by default.

### gcc tool version

Virtual machine: It is best to use the cross-compilation tool in the SDK (or go to the official website to download and install the same cross-toolchain yourself)

```
(SDK)/prebuilts/gcc/linux-x86/aarch64/gcc-arm-10.3-2021.07-x86_64-aarch64-none-linux-gnu/
```

Board side: Just install gcc, no need to cross-compile

```
$ sudo apt-get update
$ sudo apt install gcc-9
```













