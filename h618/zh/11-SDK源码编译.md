# 11-SDK源码编译






## 1. 编译环境搭建

此编译环境配置 适用 Android/Linux SDK

### 硬件要求

| 系统环境  | 要求        |
| --------- | ----------- |
| 系统版本  | Ubuntu22.04 |
| CPU核心数 | 4核以上     |
| 内存容量  | 16GB以上    |
| 硬盘容量  | 200GB以上   |



### 安装依赖软件包

```
$ sudo apt install git bc bison build-essential curl flex libsdl1.2-dev 
$ sudo apt install g++-multilib gcc-multilib gnupg gperf libncurses5-dev 
$ sudo apt install imagemagick lib32ncurses5-dev lib32readline-dev squashfs-tools 
$ sudo apt install lib32z1-dev liblz4-tool xsltproc libssl-dev libwxgtk3.0-dev 
$ sudo apt install libxml2 libxml2-utils schedtool lzop pngcrush rsync 
$ sudo apt install yasm zip zlib1g-dev python device-tree-compiler 
$ sudo apt install python-pip gawk openjdk-8-jdk u-boot-tools patchelf expect
$ sudo pip install pyelftools
```

> 软件包名称 会根据UBUNTU版本更新而变化
>
> 不同UBUNTU版本安装失败，可网络搜索对应的解决方法



## 2. Android SDK编译

### SDK介绍

Android SDK版本信息

内核版本：5.4

Android系统版本：12.0



### 获取SDK源码

* 网盘下载目录

```
1-SDK软件源码\Android12.0\
	h618-android12.0-20240320.tar.gz
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



* 合并源码压缩包

```
$ cat *tar.gz_0* > sdk.tar.gz
```

> 原始压缩包默认可能超过100G以上，所以采用分包压缩的方式
>
> 在解压前，需要先把压缩包合并起来



* 解压源码压缩包

```
$ tar -zxvf *.tar.gz
```

> 解压完成后，查看源码目录仅有.git文件夹，需要执行下一步才能完成恢复出源码



* Git恢复源码目录

```
$ git reset --hard
```



### 首次编译环境配置

首次编译需要配置一下编译环境，后续不改变sdk位置，则无需重复配置

```shell
$ cd longan
$ ./build.sh config
$ cd -
```

编译选项

```
Welcome to mkscript setup progress
All available platform:
   0. android
   1. linux
Choice [android]: 0
All available ic:
   0. h618
Choice [h618]: 0
All available board:
   0. ft
   1. p1
   2. p2
   3. p7
   4. p7l
   5. perf1
   6. perf2
   7. perf3
   8. qa
Choice [p2]: 2
All available flash:
   0. default
   1. nor
Choice [default]: 0
All available kern_ver:
   0. linux-5.4
Choice [linux-5.4]: 0
All available arch:
   0. arm
   1. arm64
Choice [arm64]: 1
```

选择主板

```
$ ./build.sh lunch
will lunch sdk

You're building on Linux
Lunch menu...pick a combo:

1. BoardConfig-kickpi-k2b-tablet.mk
2. BoardConfig-kickpi-k2b-tv.mk
3. BoardConfig-kickpi-k2c-tablet.mk
4. BoardConfig-kickpi-k2c-tv.mk

Which would you like? [0]:
```

> 说明
>
> BoardConfig-kickpi-'boadr'-'desktop'
>
> board：主板 k2b / k2c
>
> desktop：桌面 tv / tablet



### 编译完整镜像

```
$ ./build.sh
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img

注意：如果编译Android的时候失败了，没有明显的报错，可以修改build.sh 里面Android编译的进程数，将make -j64减小至你设备的实际情况。

### 单独编译分区镜像

**单独编译Uboot**

```
$ ./build.sh uboot
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



**单独编译Kernel**

```
$ ./build.sh kernel
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



**单独编译Android**

```
$ ./build.sh android
```

> 镜像生成目录：longan/out/h618_android12_p2_uart0.img



## 3.TINA Linux-SDK

> 编译线刷固件-内核4.9版本SDK

安装依赖包

```
sudo apt-get install binfmt-support qemu-user-static
```

编译命令：

```
$ ./build.sh server 
//参数可以用如下：
server)			--server版本
desktop) 		--桌面版本
kernel) 		--单独编译kernel
uboot) 			--单独编译kernel
rootfs_server)  --单独编译rootfs server版的
rootfs_desktop)	--单独编译rootfs 桌面版的
pack) 			--打包固件
```

编译固件：out/h618-p2/tina_h618-p2_uart0.img



## 4. ARMBIAN SDK



### 获取SDK源码

* 网盘下载目录

```
1-SDK软件源码\Linux\
	h618-linux-20240320.tar.gz
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

![image-20241025181221880](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181221880.png)

选择我们要构建镜像的板卡，如 **lubancat-a1 Allwinner H618**

![image-20241025181249872](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181249872.png)

选择我们要构建的根文件系统的发行版和release版本

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

选择我们要**编译**内核的板卡，如 **lubancat-a1 Allwinner H618**

![image-20241025183126142](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025183126142.png)

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

如果我们想要修改内核配置文件来开启或关闭内核的某项功能，应该如何操作:

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

