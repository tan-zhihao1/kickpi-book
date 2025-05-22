# Keybox

​	由于 efuse 空间受限， Tina 上支持了 keybox Secure Storage 功能，该功能默认开启。下面介绍如何自定义keybox进行烧录并读取。

 ## 1 固件配置

​	要想烧录Keybox的前提是安全固件，安全固件的介绍在文档[14-Secure Boot](14-Secure%20Boot.md#Generate%20secure%20firmware)

### 1.1 Keybox_list 自定义

​	uboot会根据环境变量keybox_list来选择加载至secure os中的key。 keybox_list环境变量在env文件中进行配置，使用逗号分隔各 key。  

如下：添加自定义的key名称

```diff
--- a/device/config/chips/h618/configs/default/env.cfg
+++ b/device/config/chips/h618/configs/default/env.cfg
@@ -13,7 +13,7 @@ mac=
 wifi_mac=
 bt_mac=
 specialstr=
-keybox_list=hdcpkey,widevine
+keybox_list=hdcpkey,widevine,xie
 #set kernel cmdline if boot.img or recovery.img has no cmdline we will use this
 setargs_nand=setenv bootargs earlyprintk=${earlyprintk} initcall_debug=${initcall_debug} console=${console} loglevel=${loglevel} root=${nand_root} init=${init} partitions=${partitions} cma=${cma} snum=${snum} mac_addr=${mac} wifi_mac=${wifi_mac} bt_mac=${bt_mac} selinux=${selinux} specialstr=${specialstr} gpt=1
 setargs_mmc=setenv  bootargs earlyprintk=${earlyprintk} initcall_debug=${initcall_debug} console=${console} loglevel=${loglevel} root=${mmc_root} rootwait init=${init} partitions=${partitions} cma=${cma} snum=${snum} mac_addr=${mac} wifi_mac=${wifi_mac} bt_mac=${bt_mac} selinux=${selinux} specialstr=${specialstr} gpt=1
```

### 1.2 读取Keybox配置

​	读取Keybox使用的全志提供的API，并且Tina下已经存放有这些API调用的demo。Keybox读取需要通过TA/CA。

CA：属于 Linux 端应用程序，同其他应用程序一样，编译比较简单，只需要依赖 optee‑client 所提供的库，即可编译完成。

TA：属于安全应用程序，编译需要借助 TA dev‑kit。

#### 1.2.1 TA/CA的编译配置

执行内核配置命令

```shell
$ make menuconfig
```

将下面的选项使能

```
Tina Configuration
└─> Security ‑‑‑>
	└─> OPTEE ‑‑‑>
		└─> ‑*‑ optee-client................................................ optee-client 
		└─> <*> optee-efuse-read................................... OPTEE efuse read demo
		└─> <*> optee-helloworld....................................... OPTEE Hello World
		└─> -*- optee-os-dev-kit........................................ optee-os-dev-kit  
```

> optee-efuse-read 为我们使用的示例
>
> optee-helloworld 用于确认TA/CA环境是否正常示例

查看是否修改成功

```shell
$ git diff  target/allwinner/h618-p2/defconfig
```

```diff
diff --git a/target/allwinner/h618-p2/defconfig b/target/allwinner/h618-p2/defconfig
index c6c47b5f4..d3b2737eb 100755
--- a/target/allwinner/h618-p2/defconfig
+++ b/target/allwinner/h618-p2/defconfig
@@ -4232,10 +4232,10 @@ CONFIG_PACKAGE_wpa_supplicant_rtl=y
 #
 # CONFIG_PACKAGE_optee-aes-hmac is not set
 # CONFIG_PACKAGE_optee-base64 is not set
-# CONFIG_PACKAGE_optee-client is not set
-# CONFIG_PACKAGE_optee-efuse-read is not set
-# CONFIG_PACKAGE_optee-helloworld is not set
-# CONFIG_PACKAGE_optee-os-dev-kit is not set
+CONFIG_PACKAGE_optee-client=y
+CONFIG_PACKAGE_optee-efuse-read=y
+CONFIG_PACKAGE_optee-helloworld=y
+CONFIG_PACKAGE_optee-os-dev-kit=y
 # CONFIG_PACKAGE_optee-rotpk is not set
 # CONFIG_PACKAGE_optee-secure-storage is not set
 # CONFIG_PACKAGE_optee-test is not set
```

添加符合平台版本的dev_kit

```shell
$ cd package/security/optee-os-dev-kit/dev_kit/
$ cp -rp arm-plat-sun50iw1p1 arm-plat-sun50iw9p1
```

#### 1.2.2 optee-efuse-read 对应修改介绍

​	optee-efuse-read 要能正常读取keybox并打印，需要对原本的demo进行对应的修改，增加对keybox读取到的缓存区进行打印。下面是测试时做的修改，可供参考：

```diff
--- a/package/security/optee-efuse-read/src/ta/efuse_read_demo_ta.c
+++ b/package/security/optee-efuse-read/src/ta/efuse_read_demo_ta.c

@@ -96,13 +96,16 @@ TEE_Result TA_InvokeCommandEntryPoint(void *pSessionContext,
                        memcpy(keyname,"testkey",sizeof("testkey"));
                        keyname[49]=0;
                }
-               i = utee_sunxi_keybox((const char*)keyname, rdbuf, 16);
+               printf("keyname: %s \n",keyname);
+               i = utee_sunxi_keybox((const char*)keyname, rdbuf, 128);
                if (i != TEE_SUCCESS) {
                        printf("read key:%s from keybox failed with:%d\n",keyname,i);
                        return i;
                } else {
-                       i = utee_sunxi_read_efuse("oem_secure", &rd_len,
-                                                 rdbuf + 16);
+                       printf("keybox:\n");
+                       dump(rdbuf, 128);
+                       i = utee_sunxi_read_efuse("widevine", &rd_len,
+                                                 rdbuf + 128);
                        if (i == TEE_SUCCESS) {
                                printf("read result:\n");
                                dump(rdbuf, rd_len + 16);
```

> dump为demo自带的16进制打印函数
>
> 128 为我烧写的key长度

#### 1.2.3 全志特有API的介绍

* utee_sunxi_keybox

```c
TEE_Result utee_sunxi_keybox(const char *keyname, uint8_t *out_buf, uint32_t size);
```

功能：读取 keybox 中特定名称的数据。

参数：

• keyname： key 数据名称，需与 env*.cfg 文件中 keybox_list 指定的名称相同。

• out_buf：待读取的数据存放空间，空间大小必须大于等于 size。

• size：待读取的长度。

返回值：

• 0：成功

• ‑1：失败

* utee_sunxi_read_efuse

```c
TEE_Result utee_sunxi_read_efuse(const char *keyname, uint8_t *result_len, uint8_t *rd_buf);
```

功能：读取 efuse。

参数：

keyname： key 数据名称。

result_len：返回读取数据的长度。

rd_buf：待读取的数据存放空间，空间大小必须大于等于 efuse 中数据的长度。

返回值：

0：成功

其他：失败

* utee_sunxi_write_efuse

```c
TEE_Result utee_sunxi_write_efuse(const char* keyname, uint8_t write_len, uint8_t *wr_buf);
```

功能：烧录 efuse。

参数：

keyname： key 数据名称。

write_len：烧录数据的长度，单位 byte。

wr_buf：待烧录的数据，长度必须大于等于 write_len。

返回值：

0：成功

其他：失败

### 1.3 编译安全固件

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

### 1.4 烧录确认

确保keybox_list 已经存在要烧录的key名称xie

uboot中确认，开机上电后一直按s按键进入uboot命令行界面

```
=> printenv
keybox_list=hdcpkey,widevine,xie
```

系统中确认TA/CA 正常

```shell
# tee-supplicant &
# hello_world_na 
NA:init context
NA:open session
TA:creatyentry!
TA:open session!
NA:allocate memory
NA:invoke command
TA:rec cmd 0x210
TA:hello world!
NA:finish with 0
```

## 2 烧写Keybox

​	烧写keybox方法和rotpk 一样，通过 PC 端工具 dragonSN 进行烧录。 DragonSN 工具通过 usb 与设备通信，控制设备烧录指定的 二进制keybox文件信息。具体烧录步骤如下:  

打开dragonSN ：

![image-20250318152318389](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152318389.png)

鼠标选择配置key

![image-20250318152340301](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152340301.png)

选择确定

​										![image-20250318152416786](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152416786.png)

![image-20250318152535419](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152535419.png)

鼠标右键列表可以选择删除不要的配置

![image-20250318152745256](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152745256.png)

添加配置

![image-20250318152809796](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152809796.png)

![image-20250318152837932](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152837932.png)

key详细配置如下：key name 为你Keybox_list添加的名称

![image-20250318152922597](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152922597.png)

确定添加

![image-20250318153052923](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153052923.png)

![image-20250318153112853](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153112853.png)

添加完成后返回烧录工具主界面

![image-20250318153137093](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153137093.png)

![image-20250318153220826](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153220826.png)

![image-20250318153238161](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153238161.png)

选择你要烧录的key文件

![image-20250318153520969](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153520969.png)

确定选择的文件后选择配置 擦除之前烧录 和 烧写后关机，非生产阶段不要选择自动烧录，以免烧错。

![image-20250318153626900](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153626900.png)

板子上电后，界面如下：

![image-20250318154116349](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154116349.png)

点击烧录

![image-20250318154134143](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154134143.png)

烧写中

![image-20250318154147718](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154147718.png)

烧写完成

![image-20250318154206857](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154206857.png)

完成后记得关闭工具，以免重启板子后再次识别

## 3.读取keybox

uboot中读取加密后的keybox

```
=> pst read
[259.435]name in map xie
[259.450]1 data:
48 39 25 17 00 00 00 00 78 69 65 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 01 00 00 00 
0f 82 d3 8a 00 00 00 00 00 00 00 00 00 00 00 00 
cc 00 00 00 78 69 65 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 80 00 00 00 00 00 00 00 01 00 00 00 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
3e 53 6c 79 17 a6 95 48 5e f0 46 bd a7 c6 a3 e6 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
```

系统中调用demo读取keybox

```shell
# tee-supplicant &
# efuse_read_demo_na -ext_key xie
```

```
NA:init context
NA:open session
TA:creatyentry!
TA:open session!
NA:allocate memoryTA:rec cmd 0x220

NA:invoke command
keyname: xie 
keybox:
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 
0x11 0x11 0x11 0x11 0x11 0x11 0x11 0x11 

read efuse failed with:254
NA:finish with 254
```

> 读取出的对比你烧写的key，一样就是成功了