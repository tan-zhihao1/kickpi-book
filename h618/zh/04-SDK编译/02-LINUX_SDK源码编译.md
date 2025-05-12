# 02-LINUX_SDK源码编译



### 获取SDK源码

* 网盘下载目录

```
1-SDK\Linux\
	h618-linux-xxxxxxx.tar.gz
```



* 拷贝源码压缩包到编译主机目录

  > 注意事项：
  >
  > 1. 源码不可放在共享目录下编译
  >
  > 2. 编译主机不可用ROOT账号登陆



* MD5校验文件完整性

```
$ md5sum -c *.md5
```



* 解压源码压缩包

```
$ tar -zxvf *.tar.gz
```

> 解压完成后，查看源码目录仅有.git文件夹，需要执行下一步才能完成恢复出源码



* Git恢复源码目录

```
$ git reset --hard
```





### 编译命令

编译前确保编译环境能够正常联网，编译过程中会自动下载安装所需的工具

```
$ ./build.sh
```

下面命令编译固件制作的SD卡为烧录卡

```
$ ./build.sh auto_burn=true
```

> 烧录卡想要取消自动烧录 可以通过板子SD烧录卡启动后输入系统下面命令控制
>
> 禁用自动烧录服务

```
$ sudo systemctl disable kickpi.service 
```

> 启动自动烧录服务（烧录卡默认启动）

```
$ sudo systemctl enable kickpi.service 
```

##### 编译详细步骤选择介绍

在aw-image-build根目录运行 **./build.sh** 来进入交互式构建界面

![image-20241025181130073](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181130073.png)

可以看到，在我们的功能选择界面，总共有7个选项

- Build all step ：构建完整镜像，自动执行step1到step4
- step1.Build Kernel：单独**编译**内核
- step2.Build U-boot：单独**编译**u-boot
- step3.Build base-rootfs and deb packages：构建根文件系统镜像并打包自定义deb软件包
- step4.Pack image：将系统镜像的各个部分按规则打包成一个完整的用于烧录的镜像
- update source repository：下载或更新软件源码
- clean source/build/out files：清理拉取的源代码、构建过程中产生的文件、最终输出的镜像

如果我们只是需要构建一个用于板卡启动的镜像，选择 **Build all step** 再按Enter键进入下一步

![image-20250324091854498](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324091854498.png)

选择我们要构建镜像的板卡，如kickpi-k2b Allwinner H618**

![image-20241025181249872](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181249872.png)

选择我们要构建的根文件系统的发行版和release版本，选择jammy Ubuntu

![image-20241025181345456](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181345456.png)

选择我们要构建的根文件系统是否为桌面版，如 **desktop**

![image-20241025181414687](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181414687.png)

选择我们要构建的根文件系统的桌面环境，如 **Xfce**

![image-20241025181456150](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181456150.png)

选择我们要构建的根文件系统的桌面环境的配置文件，默认是 **base configuration**

![image-20241025181509226](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181509226.png)

选择我们要构建的根文件系统预装的软件包，使用空格键选中，再按Enter键开始构建过程

构建完成后镜像保存在out/images目录下

### 单步构建镜像

对用户而言，如果要对板卡进行二次开发，需要预装应用、修改调试内核等，可能就涉及到了根文件系统或内核的单独构建， 此时就可以选择单步构建的方式。

在aw-image-build根目录运行 **./build.sh** 来进入交互式构建界面

![image-20241025182302642](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025182302642.png)

可以看到，除了使用 **Build all step** 进行一键构建以外，还有step1到step4的单步构建选项， 实际上一键构建的过程，就是顺序调用这四部分脚本的过程。

####  step1.Build Kernel

这一步是单独**编译**内核源码并生成内核deb包

运行 **./build.sh** 进入交互式构建界面，选择 **step1.Build Kernel** 再按Enter键进入下一步

![image-20241025183117623](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183117623.png)

选择我们要**编译**内核的板卡，如 **kickpi-k2b Allwinner H618**

![image-20250512093014049](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512093014049.png)

接下来就进入了内核的构建过程，在构建过程中，会有一些内核构建的提示信息

![image-20241025183152847](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183152847.png)

- Compiler kernel info：**编译**的内核版本
- Compiler kernel path：**编译**的内核保存路径
- Copy kernel config：使用的内核配置文件
- Compiler kernel command：内核构建命令
- Compiler kernel deb：内核deb包的构建命令

在构建结束后还会打印一些辅助信息

![image-20241025183417053](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183417053.png)

- Kernel deb path：构建生成的内核deb文件保存路径
- Kernel deb name：构建生成的内核deb包名称

如果我们对内核内容做了修改，此时将生成的内核deb包传输到正在运行的板卡中， 使用dpkg -i linux-***.deb，然后重启板卡，就可以更新升级内核了

如果我们想要修改内核配置文件来开启或关闭内核的某项功能，应该如何操作（需先正常编译一次）:

首先我们需要进入内核所在的目录，可以查看构建时的提示信息 **Compiler kernel path** 进入对应的目录

```
# 进入内核源码目录 
cd /home/jiawen/Allwinner/H618/aw-image-build/source/kernel/linux-5.4-h618 

# 修改内核配置文件 
make ARCH=arm64 menuconfig

# 修改完成后保存并退出menuconfig配置页面，此时修改的配置保存在.config中，我们还需要将修改 同步到板卡的内核配置文件中 

# 保存defconfig配置文件 
make ARCH=arm64 savedefconfig 

# 使用生成的defconfig文件替换板卡配置文件 
cp defconfig arch/arm64/configs/linux_h618_defconfig 

# 查看板卡配置文件修改的内容 
git diff arch/arm64/configs/linux_h618_defconfig
```

至此，板卡的内核配置文件的修改就完成了，我们可以通过构建工具来重新**编译**内核

####  step2.Build U-boot

这一步是单独**编译**u-boot并将生成的u-boot镜像打包为deb

运行 **./build.sh** 进入交互式构建界面，选择 **step2.Build U-boot** 再按Enter键进入下一步

选择我们要**编译**u-boot的板卡，如 **lubancat-a1 Allwinner H618**

如果我们的板卡支持多种版本的内核，为了配合内核的启动，就会有不同版本的u-boot， 这一步还会有**编译**分支版本的选择，选择与内核分支一致的版本，如果仅支持一个版本的话，将会使用默认版本，不会弹出选择页面。

接下来就进入了u-boot的**编译**过程，在编译时会有一些构建的提示信息

- Compiler uboot info：**编译**的u-boot版本
- Compiler uboot path：**编译**的u-boot保存路径
- Compiler uboot config：使用的u-boot配置文件
- Compiler uboot command：u-boot构建命令

结束后还会打印一些辅助信息

- Target directory：构建生成的u-boot deb文件保存路径
- File name：构建生成的u-boot deb包名称

如果我们对u-boot内容做了修改，此时将生成的u-boot deb包传输到正在运行的板卡中， 使用dpkg -i lubancat-a1-uboot-xxx.deb将u-boot镜像解压到板卡根文件系统中， 最后还需要使用脚本将u-boot镜像写入进我们存储设备的指定位置覆盖原来的u-boot。

```
# 使用管理员权限运行脚本 
sudo nand-sata-install 
```

![image-20241025183525874](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183525874.png)

选择选项 **5 Install/Update the bootloader on SD/EMMC **

![image-20241025183646097](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183646097.png)

遇到警告选yes

![image-20241025183611510](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183611510.png)

完成后，我们重启板卡即可使用更新后的u-boot

#### step3.Build base-rootfs and deb packages

这一步是单独构建基础根文件系统及本地自定义的deb软件包

运行 **./build.sh** 进入交互式构建界面，选择 **step3.Build base-rootfs and deb packages** 再按Enter键进入下一步

选择我们要构建镜像的板卡，如 **lubancat-a1 Allwinner H618**

如果我们的板卡支持多种版本的内核，为了适配不同内核的些许差异，这一步还会有**编译**分支版本的选择， 选择与内核分支一致的版本，如果仅支持一个版本的话，将会使用默认版本，不会弹出选择页面。

选择我们要构建的发行版和release版本，目前aw-image-build支持构建Debian系统和Ubuntu系统， 并且根据内核版本的不同，还可以选择不同的release版本，如Debian系：Bullseye、bookworm等； Ubuntu系统：focal、jammy等

然后就是选择我们要构建的根文件系统的类别，server版是不带桌面的版本，适合小内存，小存储，对性能要求高的用户， desktop是带桌面的版本，安装了一些常用的桌面应用，占用资源较多，但是操作较为便利，和桌面PC操作方式类似。

如果选择的是server版，接下来就进入了根文件系统的构建过程；

如果选择的是desktop版本，还需要进一步的配置。

选择要安装的桌面套件的版本，我们根据板卡资源配置的多少，已经筛选出了适合安装的版本

选择桌面套件的配置文件，一般默认 **base configuration**

选择额外的预装软件包，这些软件包都是可选的。出于对发布镜像体积的考虑，在随板卡发布的镜像中默认未安装这些额外的软件包

接下来就进入了根文件系统及部分本地deb软件包的构建过程，构建时间与网络性能和磁盘性能密切相关。

由于根文件系统的构建很耗费时间，为了减少相同内容根文件系统的重复构建，我们通过对比需要安装的软件包名称字符的哈希值来判断根文件系统是否需要重新构建。

首次构建的基础根文件系统压缩包保存在 aw-image-build/build/rootfs-base 目录下， 其命名规则是 **系统release版本-桌面套件版本-处理器架构.软件包字符串哈希值.压缩格式** , 当我们需要二次构建根文件系统时，首先计算出所有要安装的软件包的名称的字符串哈希值， 然后在存放基础根文件系统压缩包的目录下寻找是否存在对应的压缩包，如果存在的话就跳过构建过程， 直接进行下一步骤，如果不存在，就构建新的基础根文件系统并压缩保存。

完成了根文件系统的构建后，还会继续使用本地源码去预先构建要在stop4中安装到系统中的自定义deb软件包以及和保存板卡间差异内容的deb包等。

有时候也会遇到基础根文件系统内容损坏，需要强制重新构建的情况，这时将build/rootfs-base目录下的对应名称的所有文件都删除再重新**编译**即可。

#### step4.Pack image

这一步是将step1-step3生成的内容进行安装和打包，生成一个完整的用于板卡烧录的镜像。

运行 **./build.sh** 进入交互式构建界面，选择 **step3.Build base-rootfs and deb packages** 再按Enter键进入下一步

### 编译工具链

编译完成时会在此目录有编译工具链

``` shell
aw-image-build/toolchains/gcc-linaro-7.4.1-2019.02-x86_64_arm-linux-gnueabi/bin
```