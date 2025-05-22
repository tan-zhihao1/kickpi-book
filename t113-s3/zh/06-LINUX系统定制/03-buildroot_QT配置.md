# 02-buildroot系统定制

### 1.在buildroot下面，打开配置界面进行配置

```
make menuconfig //打开配置界面
make savedefconfig  //保存为默认配置
```

Buildroot下QT5配置有关如下：

```
Target packages
　　->Libraries
　　　　->Graphics
　　　　　　->libdrm
　　　　　　　　->etnaviv--支持的GPU驱动。
　　　　　　　　->Install test programs--libdrm测试程序。
    -> Graphic libraries and applications (graphic/text)
　　　　->mesa3d
　　　　　　->Gallium Etnaviv driver--Vivante GPU的Mesa驱动。
　　　　　　->Gallium swrast driver--基于Gallium3D架构实现的软件OpenGL。
　　　　　　->gbm--使能Generic Buffer Management。
　　　　　　->OpenGL EGL--EGL是OpenGL ES和底层Native平台视窗系统之间的接口。
　　　　　　->OpenGL ES--OpenGL ES (OpenGL for Embedded Systems)。
　　　　　　->
　　　　->Qt5
　　　　　　->qt5base
　　　　　　　　->Custom configuration options
　　　　　　　　->Config file
　　　　　　　　->Compile and install examples (with code)--编译安装示例程序和代码。
　　　　　　　　->gui module
　　　　　　　　　　->widgets module--Qt5Widgets库文件。
　　　　　　　　　　->OpenGL support--支持OpenGL。
　　　　　　　　　　　　->OpenGL API
　　　　　　　　　　　　->opengl module
　　　　　　　　　　->linuxfb support--使用libqlinuxfb.so作为显示插件。
　　　　　　　　　　->eglfs support--使用libqeglfs.so作为显示插件。
　　　　　　　　　　->linuxfb support--支持Linux Framebuffer插件。
　　　　　　　　　　->Default graphical platform--选择默认的图形平台。
　　　　　　　　　　->GIF support
　　　　　　　　　　->JPEG support
　　　　　　　　　　->PNG support
　　　　　　　　->Enable Tslib support--支持tslib插件。
　　　　　　->qt5charts--Qt5 chart组件。
　　　　　　->qt5declarative--支持QML脚本语言和Qt Quick。
　　　　　　->quick module
　　　　　　->qt5quickcontrols
　　　　　　->qt5quickcontrols2
　　　　　　->qt5svg
　　　　　　->qt5virtualkeyboard
　　　　　　->language layouts
```

### 2.QT5配置后编译Buildroot

out目录下就有QT5的库。

qmake位置：out/t113/evb1_auto/buildroot/buildroot/host/bin/qmake

可以使用qmake进行编译想要在板子上运行的QT-project

### 3.QT在板子上的运行

QT程序运行前需要进行QT环境变量的设置：

```
export QT_QPA_PLATFORM=linuxfb:tty=/dev/fb0 //指定我们的运行平台和输出显示屏
export QT_QPA_FB_DISABLE_INPUT=1 //添加input
```



## FAQ

1.如果遇到QT运行后显示不了中文，应该是缺少中文库wqy-zenhei.ttc，可以网上下载并push进去(QT代码字体查找的位置，一般默认是/usr/share/fonts/ )

2.ubuntu里面安装wqy-zenhei字库 `sudo apt install fonts-wqy-zenhei`
