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

第二步，生成系统 platform.pem 文件

```
$ openssl pkcs8 -in platform.pk8 -inform DER -outform PEM -out platform.pem -nocrypt
```

第三步，生成

```
$ openssl pkcs12 -export -in platform.x509.pem -inkey platform.pem -out platform.pk12 -name
```

