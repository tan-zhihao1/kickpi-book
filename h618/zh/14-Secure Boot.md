# 14-Secure Boot  

### 1.Secure Boot 原理

​	Secure Boot 启动过程中，芯片在启动时，会先对系统做安全性检验，检验通过后才引导系统。检查不通过则认为系统已经被修改，拒绝引导系统并进入烧录模式。



### 2、生成密钥

生成密钥的脚本位于: longan/build/createkeys   

```
./longan/build/createkeys 
//选择使用的平台
```

> 生成的密钥位于longan/out/$(platform)/common/keys  下

![image-20241122100503977](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122100503977.png)

**这些密钥都是相互关联的，必须配套使用。生成的密钥请成套妥善保存。**  

其中 **rotpk.bin** 为烧录到芯片中，用于验证根证书的公钥。 **rotpk.bin 需要在烧录了安全固件的设备上才能烧录到芯片中**，使用方法后述，详见 rotpk 烧写。其他为打包固件时用于为固件包签名的私钥。一个固件由多个部分组成，每个部分使用单独的密钥对进行签名认证。



**如果已经有密码，将密钥放到longan/out/$(platform)/common/keys  下**

密钥的文件数量及名称都是根据平台固件打包的过程做过适配的。 A 平台生成的密钥不可用于 B 平台安全固件的打包。否则打
包过程可能会因为找不到指定的密钥而失败。

