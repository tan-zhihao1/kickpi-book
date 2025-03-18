# 44-Keybox

​	由于 efuse 空间受限， Tina 上支持了 keybox Secure Storage 功能，该功能默认开启。下面介绍如何自定义keybox进行烧录并读取

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

## 1.2 读取Keybox配置

​	读取Keybox使用的全志提供的API，并且Tina下已经存放有这些API调用的demo。Keybox读取需要通过TA/CA。

CA：属于 Linux 端应用程序，同其他应用程序一样，编译比较简单，只需要依赖 optee‑client 所提供的库，即可编译完成。

TA：属于安全应用程序，编译需要借助 TA dev‑kit。

### 1.2.1 TA/CA的编译

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



### 1.2.2 全志特有API的介绍

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