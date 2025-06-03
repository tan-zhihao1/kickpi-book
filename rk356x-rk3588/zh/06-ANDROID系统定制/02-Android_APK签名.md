# 02-Android_APK签名



## APK签名密钥

rockchip Android 平台密钥路径

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

 -k ./platform.jks 指定生成jks文件名

-p android  指定密码

-pk8 platform.pk8 

-cert platform.x509.pem 

-alias android 指定用户名

```
$./keytool-importkeypair -k ./platform.jks -p android -pk8 platform.pk8 -cert platform.x509.pem -alias android
```

