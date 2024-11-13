## 33-Linux QT application compilation environment construction

### QT Creatorenvironment configuration

qmake tool

1.View current Linux architecture `uname -m` 

2.install dependencies

```
sudo apt-get install build-essential perl python3 git
sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev
sudo apt-get install flex bison gperf libicu-dev libxslt-dev ruby
sudo apt-get install libxcursor-dev libxcomposite-dev libxdamage-dev libxrandr-dev libxtst-dev libxss-dev libdbus-1-dev libevent-dev libfontconfig1-dev libcap-dev libpulse-dev libudev-dev libpci-dev libnss3-dev libasound2-dev libegl1-mesa-dev gperf bison nodejs
sudo apt-get install libasound2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-good1.0-dev libgstreamer-plugins-bad1.0-dev
sudo apt install clang libclang-dev
```

[qt Not in maintenance version](https://download.qt.io/new_archive/qt/)

[qt In the maintenance version](https://download.qt.io/official_releases/qt/)

It is recommended to use qt5.12.2 qtcreator 4.11.1 

choose the version you need

![image-20241028093459681](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093459681.png)

Select single

![image-20241028093254591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093254591.png)

Select  tar.xz

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

The script is to execute./configure
-prefix /opt/Qt/ \ Qt installation path
- opensource-confirm-license \ installation version, community edition or commercial edition
-nomake examples \ Do not compile installation examples
-nomake tests \ Does not compile installation tests
-release \ release Compile
-skip qt3d \ skips compiling modules
-skip qtcharts \ skips compiling modules
-skip qtandroidextras \ skips compiling modules
-skip qtlocation \ skips the compilation module, which is error-prone
-skip qtmultimedia \ skips the compilation module
-skip qtsensors \ Skip compiling modules
-skip qtserialbus \ skips compiling modules
-skip qtserialport \ skips compiling modules
-skip qtwayland \ skips compiling modules
-skip qtwebengine \ skips the compilation module, which is error-prone

qtlocation qtwebengine, if you do not specifically need these two modules, it is recommended not to compile
These commands are described in the unzipped README
After the execution, improve the system as prompted. Most of the errors are due to incomplete installation
Carry out

```
sudo make -j4
```

Long time, 4-12 hours based on board function

##### FAQ: If you have already installed python3, but the Python interpreter is missing during the build process, you can create a symbolic link:

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

final execution`qmake -v`

![image-20241028094050024](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094050024.png)

### Compile and install qtcreator

To install qtcreator, you must first install Qt and compile qtcreator with Qt. If there is an error in compiling Qt, the compilation at this location will also error. If there are uncompiled modules or undependent modules when Qt is compiled, the qt compilation will not be affected, but the qtcreator will be affected. I tried adding -no-opengl\, and there is no error in compiling qt. You can install it, but the qtcreator will fail.

Qtcreator should preferably correspond to the Qt version
[Qtcreator source code](https://download.qt.io/archive/qtcreator/)

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

After execution, open the bin folder and there will be qtcreator and qtcreator.sh
Execute./qtcreator.sh &
This way qtcreator will run in the background.
Finally, you can configure qtcreator.
Tools - > Options - > Kits Kits Select gcc as the compilation toolchain corresponding to the board. Generally, qtcreator is equipped with the gcc tool version by default, and select the cross-compilation tool corresponding to uname -r. It is best to use the cross-compilation tool with the same version number in the SDK.

![image-20241028095022662](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028095022662.png)









