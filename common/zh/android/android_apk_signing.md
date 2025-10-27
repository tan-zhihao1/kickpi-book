# Android_Apk_Signing

本文介绍如何对 APK 进行系统签名。



## 获取APK签名密钥

> 可从主板平台源码路径获取密钥，或从对应主板平台的网盘中获取

**A133 Android SDK 源码密钥路径**

```
(SDK)$ ls android/build/target/product/security/
platform.pk8 platform.x509.pem
```

**H618/RK3576 Android SDK 源码密钥路径**

```
(SDK)$ ls build/target/product/security/
platform.pk8 platform.x509.pem
```

**RK3562/RK3568/RK3588 Android SDK 源码密钥路径**

```
(SDK)$ ls device/rockchip/common/security/
 platform.pk8     platform.x509.pem  
```

**网盘路径**

```
3-SoftwareData/Android_app_signkey/*key*
```



## 获取 keytool-importkeypair 工具

**网盘路径**

```
3-SoftwareData/Android_app_signkey/
```



## 制作平台密钥库JKS

第一步，存放系统签名文件和 keytool-importkeypair 到同一级目录下。

```
$ mkdir sign_key/
$ ls sign_key/
platform.pk8	platform.x509.pem	keytool-importkeypair
```



第二步，使用 keytool 生成 jks 文件。

`keytool-importkeypair` 用法：

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

在源码中进行重签（确保已经编译过源码）。

第一步，配置 Android java 环境。

H618 平台

```
$ source build/envsetup.sh
$ source device/softwinner/.BoardConfig.mk
$ lunch apollo_p2-userdebug
```

A133 平台

```
$ source build/envsetup.sh
$ lunch ceres_c3-userdebug
```

RK3562 / RK3568 / RK3576 / RK3588 平台

```
$ ./build.sh lunch 
$ source device/rockchip/.BoardConfig.mk
$ source build/envsetup.sh 
$ lunch $TARGET_PRODUCT-$BUILD_VARIANT
```

第二步，利用 java 进行签名。

> 不同平台根据实际签名文件路径，替换  platform.pk8 platform.x509.pem 下面命令路径。

```shell
$ java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" \
-jar out/host/linux-x86/framework/signapk.jar  --disable-v2 \
-w device/rockchip/common/security/platform.x509.pem \
device/rockchip/common/security/platform.pk8 \
old.apk new.apk
```

> - `java`：启动 Java 虚拟机（JVM）的命令。
> - `-Xmx2048m`：设置 JVM 最大堆内存为 2048MB（2GB），避免签名过程中因内存不足报错。
> - `-Djava.library.path="out/host/linux-x86/lib64"`：指定 Java native 库（C/C++ 编写的库）的搜索路径，这里指向 Android 编译输出的主机端（Linux x86 架构）库目录，确保签名工具能加载依赖的原生库。
>
> - `-jar out/host/linux-x86/framework/signapk.jar`：指定执行 `signapk.jar` 这个工具（Android 源码中用于 APK 签名的官方工具，位于编译输出的主机端框架目录）。
>
> - ` --disable-v2 `
>
>   禁用 APK 签名方案 V2（仅使用 V1 签名）。
>
>   - V1 是基于 JAR 的传统签名（生成 `META-INF` 目录下的签名文件），兼容性好但安全性较低；
>   - V2 是 Android 7.0+ 引入的整体 APK 签名，安全性更高。
>     禁用 V2 通常是为了兼容旧系统或特定场景（如某些定制系统对 V2 签名支持不完善）
>
> - `-w platform.x509.pem`：指定 **公钥证书文件**（`platform.x509.pem`），`-w` 表示验证证书链的完整性。
> - `platform.pk8`：指定 **私钥文件**（`platform.pk8`），与上述公钥证书配对，用于实际签署 APK。
>
> - `old.apk`：待签名的原始 APK 文件（可以是未签名、预签名或其他签名的 APK）。
> - `new.apk`：签名后生成的新 APK 文件（保存路径和文件名，将覆盖原文件同名文件）。



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

