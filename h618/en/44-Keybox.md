# 44-Keybox

​	Due to the limited efuse space, the keybox Secure Storage function is supported on Tina, which is enabled by default. The following describes how to customize the keybox to burn and read

 ## 1 firmware configuration

​	To burn the Keybox, the premise is the security firmware. The introduction of the security firmware is in the document.[14-Secure Boot](14-Secure%20Boot.md#Generate%20secure%20firmware)

### 1.1 Keybox_list

​	Uboot will select keys to be loaded into the secure OS according to the environment variable keybox_list. The keybox_list environment variable is configured in the env file, and each key is separated by a comma.

As follows: Add custom key name

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

### 1.2 Read Keybox Configuration

​	Read the API provided by Allwinner used by Keybox, and there is already a demo of these API calls stored under Tina. Keybox reading needs to go through TA/CA. 

CA: belongs to the Linux-side application, like other applications, the compilation is relatively simple, only need to rely on the library provided by optee-client to compile. 

TA: belongs to the security application, and the compilation needs to use TA dev-kit.

#### 1.2.1 Compilation configuration of TA/CA

Execute kernel configuration commands

```shell
$ make menuconfig
```

Enable the following options

```
Tina Configuration
└─> Security ‑‑‑>
	└─> OPTEE ‑‑‑>
		└─> ‑*‑ optee-client................................................ optee-client 
		└─> <*> optee-efuse-read................................... OPTEE efuse read demo
		└─> <*> optee-helloworld....................................... OPTEE Hello World
		└─> -*- optee-os-dev-kit........................................ optee-os-dev-kit  
```

> Opt-efuse-read for our example
>
> Example of opte-helloworld used to confirm whether the TA/CA environment is normal

Check if the modification was successful.

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

Add platform version compliant dev_kit

```shell
$ cd package/security/optee-os-dev-kit/dev_kit/
$ cp -rp arm-plat-sun50iw1p1 arm-plat-sun50iw9p1
```

#### 1.2.2 opte-efuse-read corresponding modification introduction

​	 For opte-efuse-read to read the keybox and print normally, it is necessary to modify the original demo accordingly and increase the buffer area read by the keybox for printing. The following are the modifications made during testing, for reference:

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

> dump(); The hexadecimal printing function that comes with the demo
>
> 128 The length of the key burned for me

#### 1.2.3 Introduction to Allwinner's unique API

* utee_sunxi_keybox

```c
TEE_Result utee_sunxi_keybox(const char *keyname, uint8_t *out_buf, uint32_t size);
```

Function: Read the data of a specific name in the keybox.

Parameter:

Keyname: The key data name, which should be the same as the name specified keybox_list in the env * .cfg file.

out_buf: The data storage space to be read must be greater than or equal to size.

Size: The length to be read.

Return value:

0: success

1: Failure

* utee_sunxi_read_efuse

```c
TEE_Result utee_sunxi_read_efuse(const char *keyname, uint8_t *result_len, uint8_t *rd_buf);
```

Function: Read efuse.

Parameter:

Keyname: key data name.

result_len: Returns the length of the read data.

rd_buf: The data storage space to be read must be greater than or equal to the length of the data in the efuse.

Return value:

0: success

Other: failure

* utee_sunxi_write_efuse

```c
TEE_Result utee_sunxi_write_efuse(const char* keyname, uint8_t write_len, uint8_t *wr_buf);
```

Function: Burn efuse.

Parameter:

Keyname: key data name.

write_len: The length of the burned data in bytes.

wr_buf: The length of the data to be burned must be greater than or equal to write_len.

Return value:

0: success

Other: failure

### 1.3 Compile security firmware

Configuring burn_key properties 
Set the burn_key attribute value to 1, the device will receive the information transmitted by DragonSN through usb and perform the corresponding burning work. This attribute is in the file longan/device/config/chips/h618/configs/p2/sys_config .fex, under the item [target], as shown in the figure. If not explicitly configured, press burn_key = 0 to process. 

![image-20241122101659346](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122101659346.png)

```
$ source build/envsetup.sh
$ lunch apollo_p2-userdebug
$ pack -sv
```

> The generated secure firmware is located at.：longan/out/  h618_android12_p2_uart0_secure_secure_v0.img 
>
> The v0 at the back is the version number you set, and the factory default of the chip is 0.

### 1.4 burn confirmation

确保keybox_list 已经存在要烧录的key名称

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

​	烧写keybox方法和rotpk 一样，通过 PC 端工具 dragonSN 进行烧录。 DragonSN 工具通过 usb 与设备通信，控制设备烧录指定的 keybox信息。具体烧录步骤如下:  

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