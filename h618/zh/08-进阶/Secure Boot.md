# Secure Boot  



## 1、Secure Boot 原理

​	Secure Boot 启动过程中，芯片在启动时，会先对系统做安全性检验，检验通过后才引导系统。检查不通过则认为系统已经被修改，拒绝引导系统并进入烧录模式。





## 2、密钥使用说明

密钥使用分为两种情况

1. 自己没有密钥，SDK下自行生成
2. 已有现成密钥，提供到SDK指定目录



### 2.1、生成密钥

生成密钥的脚本位于: longan/build/createkeys

```
$ ./longan/build/createkeys 
//选择使用的平台 
//如:h618

$ cp longan/out/h618/common/keys/* android/vendor/security/toc_keys/ -rf
```

> 生成的密钥位于longan/out/$(platform)/common/keys 
>
> 如：longan/out/h618/common/keys 

![image-20241122100503977](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122100503977.png)

**这些密钥都是相互关联的，必须配套使用。生成的密钥请成套妥善保存。**  

其中 **rotpk.bin** 为烧录到芯片中，用于验证根证书的公钥。 **rotpk.bin 需要在烧录了安全固件的设备上才能烧录到芯片中**，使用方法后述，详见 rotpk 烧写。其他为打包固件时用于为固件包签名的私钥。一个固件由多个部分组成，每个部分使用单独的密钥对进行签名认证。



### 2.2、使用已有密钥

**如果已经有密码，将密钥放到longan/out/$(platform)/common/keys  下**

密钥的文件数量及名称都是根据平台固件打包的过程做过适配的。 A 平台生成的密钥不可用于 B 平台安全固件的打包。否则打
包过程可能会因为找不到指定的密钥而失败。



## 3、防回滚版本号

芯片在引导固件的时候，会对比固件的版本号与芯片内存保留的版本号。

防回滚版本号在 longan/devices/configs/chips/${chip}/configs/default/version_base.mk中进行配置，文件中主要有两个属性可配置:

![image-20241122101027941](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122101027941.png)

- ROOT_ROLLBACK_USED

是否填充供 BROM 使用的返回滚版本号，平台相关，已经配置，使用默认值即可

- MAIN_VERSION

固件的防回滚版本号，可用范围为 0-31。配置其他值芯片会直接认为固件版本号检验失败。



## 4、生成安全固件<a id='Generate secure firmware'> </a>

配置burn_key 属性  

​	设置 burn_key 属性值为 1 后，设备才会接收 DragonSN 通过 usb 传输的信息，进行相应的烧录工作。该属性在文件 longan/device/config/chips/h618/configs/p2/sys_config.fex中， [target] 项下，如图。如果未显式配置，按 burn_key=0 处理。  

![image-20241122101659346](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122101659346.png)

```
$ source build/envsetup.sh
$ lunch apollo_p2-userdebug
$ pack -sv
```

> 生成的安全固件位于：longan/out/  h618_android12_p2_uart0_secure_secure_v0.img 
>
> 后面的v0就是你设置的版本号，芯片出厂默认是0



## 5、ROTPK 烧写

​	Rotpk 通过 PC 端工具 dragonSN 进行烧录。 DragonSN 工具通过 usb 与设备通信，控制设备烧录指定的 rotpk 信息。具体烧录步骤如下:  

​	1.打包的安全固件进行烧录（注意：烧录了安全固件后就不能烧录普通固件，如果烧录了普通固件会在启动最后打印提示：need secure firmware）

​	可以通过串口日志 secure enable bit: 判断是否是安全固件

​	0：普通固件，1：安全固件

​	2.在 PC 端配置 DrangonSN 工具后运行。  

​	3、工具的配置

![image-20241122102647734](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122102647734.png)

​							鼠标右键打开菜单栏

![image-20241122103219298](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122103219298.png)

![image-20241122103411818](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122103411818.png)

![image-20241122103517802](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122103517802.png)

![image-20241122103904903](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122103904903.png)

![image-20241122104243353](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122104243353.png)

​		4.PC通过USB接好板子或者板子重新复位（板子得关机重启中DrangonSN 工具才能识别到）

![image-20241122104814617](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122104814617.png)

​		5.烧写成功

![image-20241122104853169](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122104853169.png)

​	**FAQ：只能烧写一次，烧写失败如下：**

![image-20241122105405421](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122105405421.png)



## 6、校验烧写是否成功

​	串口调试接上，启动中，按住s 等待进入uboot

![image-20241122105809805](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122105809805.png)

输入`printenv` 打印uboot的环境变量 

rotpk_status=1

0： 表示没有烧录

1：表示烧录了rotpk

![image-20241122110113103](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122110113103.png)