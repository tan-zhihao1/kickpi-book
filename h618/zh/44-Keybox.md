# 44-Keybox

​	由于 efuse 空间受限， Tina 上支持了 keybox Secure Storage 功能，该功能默认开启。下面介绍如何自定义keybox进行烧录并读取

 ## 1 Keybox的烧录

​	要想烧录Keybox的前提是安全固件，安全固件的介绍在文档[14-Secure Boot](14-Secure%20Boot.md)

### 1.1 Keybox的自定义

​	创建自定义的Keybox得是二进制文件，因为最终烧录进去的只会是二进制文件。

### 1.2 Keybox的烧录过程

