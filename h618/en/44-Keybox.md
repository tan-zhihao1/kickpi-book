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

Make sure keybox_list key name to burn already exists

Confirm in uboot, keep pressing the s button after booting and powering on to enter the uboot command line interface.

```
=> printenv
keybox_list=hdcpkey,widevine,xie
```

The system confirms that TA/CA is normal.

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

## 2 Burn Keybox

​	The method of burning the keybox is the same as that of rotpk. It is burned through the PC-side tool dragonSN. The dragonSN tool communicates with the device through USB and controls the device to burn the specified keybox information. The specific burning steps are as follows:

Open dragonSN.

![image-20250318152318389](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152318389.png)

Mouse selection configuration key

![image-20250318152340301](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152340301.png)

Select OK(确定)

​										![image-20250318152416786](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152416786.png)

![image-20250318152535419](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152535419.png)

Right mouse button list can choose to delete unwanted configurations

![image-20250318152745256](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152745256.png)

Add configuration

![image-20250318152809796](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152809796.png)

![image-20250318152837932](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152837932.png)

The detailed configuration of key is as follows: key name is the name you Keybox_list add

![image-20250318152922597](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152922597.png)

OK Add

![image-20250318153052923](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153052923.png)

![image-20250318153112853](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153112853.png)

After the addition is completed, return to the main interface of the burning tool

![image-20250318153137093](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153137093.png)

![image-20250318153220826](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153220826.png)

![image-20250318153238161](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153238161.png)

Select the key file you want to burn

![image-20250318153520969](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153520969.png)

Select the configuration after confirming the selected file, burn before erasing, and shut down after programming. Do not choose automatic burning in the non-production stage to avoid burning mistakes.

![image-20250318153626900](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153626900.png)

After the board is powered on, the interface is as follows:

![image-20250318154116349](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154116349.png)

Click to write

![image-20250318154134143](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154134143.png)

Burning

![image-20250318154147718](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154147718.png)

Programming is complete

![image-20250318154206857](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154206857.png)

Remember to close the tool after finishing, so as not to identify it again after restarting the board.

## 3.Read keybox

Read the encrypted keybox in uboot

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

Call the demo in the system to read the keybox.

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

> The key you read is the same as the key you burned