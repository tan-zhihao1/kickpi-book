# 02-Android_APK签名

主要介绍如何对APK进行系统签名。



## 获取APK签名密钥

rockchip Android SDK 源码密钥路径

```
(SDK)$ ls device/rockchip/common/security/
 platform.pk8     platform.x509.pem  
```

网盘密钥路径

```
3-SoftwareData/Android_APK_Signature
```



## 获取 keytool-importkeypair 工具

网盘路径

```
3-SoftwareData/Android_APK_Signature
```



## 制作平台密钥库JKS

第一步，存放系统签名文件和 keytool-importkeypair 到同一级目录下。

```
(SDK)$ mkdir sign_key/
(SDK)$ cp device/rockchip/common/security/platform.pk8 sign_key/
(SDK)$ cp device/rockchip/common/security/platform.x509.pem sign_key/
(SDK)$ cd sign_key/
```



第二步，使用 keytool 生成 jks 文件。

keytool-importkeypair 用法

```
keytool-importkeypair: Missing option, exiting...
usage: keytool-importkeypair [-k keystore] [-p storepass]
-pk8 pk8 -cert cert -alias key_alias

This script is used to import a key/certificate pair
into a Java keystore.

If a keystore is not specified then the key pair is imported into
~/.keystore in the user's home directory.

The passphrase can also be read from stdin.
```

示例：

```
$./keytool-importkeypair -k ./platform.jks -p android -pk8 platform.pk8 -cert platform.x509.pem -alias android
```

>  `-k ./platform.jks` 指定生成 jks 文件名为 platform.jks
>
> `-p android`  指定密码为 android
>
> `-pk8 platform.pk8` 指定 pk8 文件路径
>
> `-cert platform.x509.pem` 指定 pem 文件路径
>
> `-alias android` 指定别名为 android



## 对APK进行系统签名

### Android Studio 方式

第一步，Build ->  Generate Signed App 选择生成带签名的APK；

![image-20250603141117161](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250603141117161.png)

第二步，选择生成APK；（App Bundle 生成的是 aab 文件）

![image-20250603141754390](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250603141754390.png)

第三步，选择系统 jks，输入对应的别名和密码。

![image-20250603142034270](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250603142034270.png)

### apksigner 方式

apksigner 用法

```
USAGE: apksigner <command> [options]
       apksigner --version
       apksigner --help

EXAMPLE:
       apksigner sign --ks release.jks app.apk
       apksigner verify --verbose app.apk

apksigner is a tool for signing Android APK files and for checking whether
signatures of APK files will verify on Android devices.

        COMMANDS
rotate                Add a new signing certificate to the SigningCertificateLineage

sign                  Sign the provided APK

verify                Check whether the provided APK is expected to verify on
                      Android
                      
lineage               Modify the capabilities of one or more signers in an existing
                      SigningCertificateLineage
                      
version               Show this tool's version number and exit

help                  Show this usage page and exit
```

示例：

```
$ apksigner sign --ks platform.jks --ks-key-alias android --out app-signed.apk app-debug.apk 
```

>  `--ks platform.jks` 指定 jks 文件路径
>
> `--ks-key-alias android`  与 jks 中的别名一致
>
> `--out app-signed.apk` 指定签名后apk的命名
>
> `app-debug.apk` 指定需要签名的apk路径



### java 方式

源码中进行重签

```shell
$ cd rk-android13.0/
$ java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" \
-jar out/host/linux-x86/framework/signapk.jar  --disable-v2 \
-w device/rockchip/common/security/platform.x509.pem \
device/rockchip/common/security/platform.pk8 \
old.apk new.apk
```

> `old.apk` 指定需要签名的apk路径
>
> `new.apk` 指定签名后的apk



### Android.mk 方式

添加 platform 配置

```
LOCAL_CERTIFICATE := platform
```

示例：

```makefile
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := Test
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_TAGS := optional
LOCAL_BUILT_MODULE_STEM := package.apk
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
LOCAL_CERTIFICATE := platform
LOCAL_SRC_FILES := Test.apk
include $(BUILD_PREBUILT)
```



## 查看APK签名信息

```
$ apksigner verify -v --print-certs test.apk
```

