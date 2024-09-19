# 30-Android13.0系统定制





## 1. 开机LOGO定制

开机LOGO分两个部分，分别是Uboot阶段、Kernel阶段

Uboot阶段解析图片kernel-5.10/logo.bmp

Kernel阶段解析图片kernel-5.10/logo_kernel.bmp

```
$ ls kernel-5.10/
	logo.bmp
	logo_kernel.bmp
```





## 2. 开机动画定制

```
$ ls device/rockchip/common/bootanimation.zip
```





## 3. 默认屏幕方向

### 修改显示方向

```
$ vim device/rockchip/rk356x/BoardConfig.mk

	SF_PRIMARY_DISPLAY_ORIENTATION := 0
```



### 修改触摸方向







## 4. 默认隐藏状态栏





## 5. 默认隐藏导航栏





## 6. 内置第三方APP





## 7. 开机自启动APP





## 8. 默认语言

```
$ vim build/target/product/full_base.mk
	PRODUCT_LOCALES := en_US
```



| 选项  | 描述 |
| ----- | ---- |
| en_US | 英文 |
| zh_CN | 中文 |
|       |      |



## 9. 默认时区





## 10. 默认永不休眠

```
$ vim device/rockchip/rk356x/overlay/frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<integer name="def_screen_off_timeout">2147483647</integer>
```



## 11. 默认禁止锁屏

```
$ vim frameworks/base/packages/SettingsProvider/res/values/defaults.xml
	<bool name="def_lockscreen_disabled">true</bool>
```



## 12. 签名APK



**SDK源码中进行重签**

```
$ cd rk-android13.0/
$ java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" \
-jar out/host/linux-x86/framework/signapk.jar  --disable-v2 \
-w build/target/product/security/platform.x509.pem \
build/target/product/security/platform.pk8 \
old.apk new.apk
```



**使用签名相关文件重签**

SDK中需要的签名相关文件

```
out/host/linux-x86/lib64	// 避免Java库问题
out/host/linux-x86/framework/signapk.jar
build/target/product/security/platform.x509.pem
build/target/product/security/platform.pk8
```



根据提供文件进行重签，具体路径自行调整

```
签名包 rk-android13.0-key.tar.gz
$ tar -xvf rk-android13.0-key-20240919.tar.gz
$ ls rk-android13.0-key
$ java -Xmx2048m -Djava.library.path="rk-android13.0-key/lib64" \
-jar rk-android13.0-key/signapk.jar  --disable-v2 \
-w rk-android13.0-key/platform.x509.pem \
rk-android13.0-key/platform.pk8 \
old.apk new.apk
```
