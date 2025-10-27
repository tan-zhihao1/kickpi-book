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

##### 编译详细步骤选择介绍

在aw-image-build目录运行 **./build.sh** 来进入交互式构建界面

![image-20241025181130073](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181130073.png)

可以看到，在我们的功能选择界面，总共有7个选项

- Build all step ：构建完整镜像，自动执行step1到step4
- step1.Build Kernel：单独**编译**内核
- step2.Build U-boot：单独**编译**u-boot
- step3.Build base-rootfs and deb packages：构建根文件系统镜像并打包自定义deb软件包
- step4.Pack image：将系统镜像的各个部分按规则打包成一个完整的用于烧录的镜像
- clean source/build/out files：清理拉取的源代码、构建过程中产生的文件、最终输出的镜像

如果我们只是需要构建一个用于板卡启动的镜像，选择 **Build all step** 再按Enter键进入下一步

![image-20250324091854498](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324091854498.png)

选择我们要构建镜像的板卡，如kickpi-k2b Allwinner H618

![image-20250902155216930](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250902155216930.png)

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



在构建结束后还会打印一些辅助信息

![image-20250512095408332](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512095408332.png)

- Kernel deb path：构建生成的内核deb文件保存路径
- Kernel deb name：构建生成的内核deb包名称

将生成的内核deb包传输到正在运行的板卡中， 使用dpkg -i linux-***.deb，然后重启板卡，就可以更新内核了

**修改内核配置项**

如果我们想要修改内核配置文件来开启或关闭内核的某项功能，应该如何操作（需先正常编译一次）:

首先我们需要进入内核所在的目录，可以查看构建时的提示信息 **Compiler kernel path** 进入对应的目录

```
# 进入内核源码目录 
cd /home/jiawen/Allwinner/H618/aw-image-build/source/kernel/linux-5.4-h618 

# 加载配置文件
make ARCH=arm64 linux_h618_defconfig

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

![image-20250512095441502](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512095441502.png)

选择我们要**编译**u-boot的板卡，如 **kickpi-k2b Allwinner H618**

![image-20250512093014049](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512093014049.png)

结束后还会打印一些辅助信息

![image-20250512095830562](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250512095830562.png)

- Target directory：构建生成的u-boot deb文件保存路径
- File name：构建生成的u-boot deb包名称

将生成的u-boot deb包传输到正在运行的板卡中， 使用dpkg命令安装， 

安装deb包

``` 
dpkg -i kickpi-k2b-uboot-currentxxxx.deb
```

最后还需要使用脚本将u-boot镜像写入进我们存储设备的指定位置覆盖原来的u-boot。

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



### 修改设备树

**设备树路径**

``` 
aw-image-build/source/kernel/linux-5.4-h618/arch/arm64/boot/dts/sunxi
sun50iw9-kickpi-k2b.dts
sun50iw9-kickpi-k2c.dts
```

只修改设备树的话
1.先编译内核
2.再编译uboot生成kickpi-k2b-uboot-currentxxxx.deb
3.板卡dpkg -i kickpi-k2b-uboot-currentxxxx.deb
4.执行nand-sata-install选择”Install/Update the bootloader on SD/eMMC“更新uboot
5.重启



### 编译工具链

编译完成时会在此目录有编译工具链

``` shell
aw-image-build/toolchains/gcc-linaro-7.4.1-2019.02-x86_64_arm-linux-gnueabi/bin
```



### 应用工具添加

添加自己的应用和工具可放在这个目录下

``` 
aw-image-build/external/packages/bsp/common
```







## 常见问题

编译cec驱动失败

![image-20250820150033592](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250820150033592.png)

参考以下修改

``` c
--- a/source/kernel/linux-5.4-h618/drivers/media/cec/cec-pin.c
+++ b/source/kernel/linux-5.4-h618/drivers/media/cec/cec-pin.c
@@ -42,7 +42,7 @@
 /* when polling for a state change, sample once every 50 microseconds */
 #define CEC_TIM_SAMPLE                 50
 
-#define CEC_TIM_LOW_DRIVE_ERROR                (1.5 * CEC_TIM_DATA_BIT_TOTAL)
+#define CEC_TIM_LOW_DRIVE_ERROR                (3/2 * CEC_TIM_DATA_BIT_TOTAL)
 
 /*
  * Total data bit time that is too short/long for a valid bit,
```

