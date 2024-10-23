# Buildroot_QT5配置

### 1.在buildroot下面，打开配置界面进行配置

```
make menuconfig //打开配置界面
make savedefconfig  //保存为默认配置
```

Buildroot下配置libdrm、mesa3d、Qt5：

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

