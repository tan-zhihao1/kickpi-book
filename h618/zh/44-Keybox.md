# 44-Keybox

​	由于 efuse 空间受限， Tina 上支持了 keybox Secure Storage 功能，该功能默认开启。下面介绍如何自定义keybox进行烧录并读取

 ## 1 烧写Keybox的固件配置

​	要想烧录Keybox的前提是安全固件，安全固件的介绍在文档[14-Secure Boot](14-Secure%20Boot.md)

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

### 





