# 02-Android_APK签名



## 获取APK签名密钥

rockchip Android SDK 密钥路径

```
(SDK)$ ls device/rockchip/common/security/
bluetooth.pk8       media.pk8         networkstack.pk8       platform.pk8       sdk_sandbox.pk8       shared.pk8       testkey.pem
bluetooth.x509.pem  media.x509.pem    networkstack.x509.pem  platform.x509.pem  sdk_sandbox.x509.pem  shared.x509.pem  testkey.pk8
media.pem           networkstack.pem  platform.pem           sdk_sandbox.pem    shared.pem            sig_gen.sh       testkey.x509.pem
```



## 制作平台密钥库JKS

第一步，存放系统签名文件到同一级目录下。

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

>  -k ./platform.jks 指定生成 jks 文件名为 platform.jks
>
> -p android  指定密码为 android
>
> -pk8 platform.pk8 指定 pk8 文件路径
>
> -cert platform.x509.pem 指定 pem 文件路径
>
> -alias android 指定别名为 android



## 对APK进行系统签名

### android studio 方式

第一步，Build ->  Generate Signed App 选择生成带签名的APK；

![image-20250603141117161](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250603141117161.png)

第二步，选择生成APK；（App Bundle 生成的是 aab 文件）

![image-20250603141754390](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250603141754390.png)

第三步，选择系统 jks，输入对应的别名和密码。

![image-20250603142034270](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250603142034270.png)

### apksigner 方式

```
$ apksigner sign --ks platform.jks --ks-key-alias android --out app-signed.apk app-debug.apk 
```

