## 02-Linux_QT开发环境搭建

### QT Creator环境配置

qmake

1.查看当前Linux系统架构 uname -m 

2.安装依赖

```
sudo apt-get install build-essential perl python3 git
sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev
sudo apt-get install flex bison gperf libicu-dev libxslt-dev ruby
sudo apt-get install libxcursor-dev libxcomposite-dev libxdamage-dev libxrandr-dev libxtst-dev libxss-dev libdbus-1-dev libevent-dev libfontconfig1-dev libcap-dev libpulse-dev libudev-dev libpci-dev libnss3-dev libasound2-dev libegl1-mesa-dev gperf bison nodejs
sudo apt-get install libasound2-dev libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-good1.0-dev libgstreamer-plugins-bad1.0-dev
sudo apt install clang libclang-dev
```

推荐使用qt5.12.2 qtcreator 4.11.1

选择你需要的版本

![image-20241028093459681](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093459681.png)

下面分虚拟机和板子两种选法：

虚拟机Ubuntu选
![1739790413797](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/1739790413797.png)

​	这个直接放到板子运行 安装软件安装你要的库即可。

ARM 选择single

![image-20241028093254591](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093254591.png)

 然后选择tar.xz

![image-20241028093434074](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028093434074.png)

下载后进行解压在opt文件夹中

```
sudo tar -xvf qt-everywhere-src-5.14.2.tar.xz -C /opt/
```

解压完成后进入文件夹，并建立auto.sh脚本文件。

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

脚本是为了执行./configure
-prefix /opt/Qt/ \ Qt安装路径
-opensource -confirm-license \ 安装版本，社区版还是商业版
-nomake examples \ 不编译安装例子
-nomake tests \ 不编译安装测试
-release \ release编译
-skip qt3d \ 跳过编译模块
-skip qtcharts \ 跳过编译模块
-skip qtandroidextras \ 跳过编译模块
-skip qtlocation \ 跳过编译模块，此模块容易出错
-skip qtmultimedia \ 跳过编译模块
-skip qtsensors \ 跳过编译模块
-skip qtserialbus \ 跳过编译模块
-skip qtserialport \ 跳过编译模块
-skip qtwayland \ 跳过编译模块
-skip qtwebengine \ 跳过编译模块，此模块容易出错

qtlocation qtwebengine ，如果不是特别需要这两个模块，建议放弃编译
这些命令在解压后的README中有说明
执行后根据提示进行改进，大部分提示错误是依赖安装不全
执行

```
sudo make -j4
```

时间较长，基于板子功能4-12小时

##### FAQ ：如果已经安装python3，但是构建过程中提示缺少了 Python 解释器。可以创建一个符号链接：

```
sudo ln -s /usr/bin/python3 /usr/bin/python
```

完成后执行

```
sudo make install
```

此命令会将程序安装在/opt/Qt/ 中。
安装成功后修改环境变量：

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

最后执行`qmake -v`

![image-20241028094050024](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094050024.png)

### 编译安装qtcreator

安装qtcreator必须先安装Qt，用Qt编译qtcreator，如果编译Qt出错，这个位置编译也会出错。如果Qt编译时，有未编译的模块或者含有不依赖的模块，qt编译不受影响，但是qtcreator会有影响。我试过加-no-opengl \，编译qt没有错误，安装也可以，编译qtcreator出错。

Qtcreator最好和Qt版本相对应
[Qtcreator源码](https://download.qt.io/archive/qtcreator/)

![image-20241028094210629](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028094210629.png)

下载完成后解压
这里默认上面教程以完成好以后：
进入解压路径，执行

```
qmaek -r
```

等待完成后生成MakeFile文件。
然后执行

```
sudo make -j4
```

等待编译完成后，执行

```
sudo make install
```

执行完

打开bin文件夹就会有qtcreator和qtcreator.sh
执行`./qtcreator.sh &`
这样qtcreator会在后台运行。
最后配置qtcreator即可。
工具—>选项—>Kits

Kits选择gcc为板子对应的编译工具链即可，一般qtcreator默认设配好

gcc工具版本

选择uname -r对应的交叉编译工具即可

最好使用SDK里面相同版本号的交叉编译工具

![image-20241028095022662](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241028095022662.png)









