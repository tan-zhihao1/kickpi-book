# Keybox

Due to limited efuse space, Tina supports the Keybox Secure Storage feature by default. This document describes how to customize and burn keyboxes, as well as read them.

---

## 1. Firmware Configuration

**Prerequisite**: Secure firmware is required. For secure firmware details, refer to [14-Secure Boot](14-Secure%20Boot.md#Generate%20secure%20firmware).

### 1.1 Customize Keybox List

U-Boot loads keys into Secure OS based on the `keybox_list` environment variable. Configure this variable in the `env.cfg` file using comma-separated key names.  

**Example**: Add a custom key name `xie`:  

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

---

### 1.2 Keybox Configuration Reading

Keybox reading uses Allwinner-provided APIs. Tina includes demo implementations for these APIs. Keybox access requires TA/CA interaction.

- **CA**: A Linux user-space application dependent on `optee-client` libraries.
- **TA**: A secure application compiled using TA dev-kit.

#### 1.2.1 TA/CA Compilation Configuration

1. Run kernel configuration:  
   ```shell
   $ make menuconfig
   ```
2. Enable the following options:  
   ```
   Tina Configuration
   └─> Security ‑‑‑>
       └─> OPTEE ‑‑‑>
           └─> -*- optee-client............................... optee-client 
           └─> <*> optee-efuse-read................... OPTEE efuse read demo
           └─> <*> optee-helloworld....................... OPTEE Hello World
           └─> -*- optee-os-dev-kit...................... optee-os-dev-kit  
   ```
   > `optee-efuse-read`: Example for keybox reading.  
   > `optee-helloworld`: Example to verify TA/CA environment.

3. Verify changes:  
   ```shell
   $ git diff target/allwinner/h618-p2/defconfig
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

4. Add platform-specific dev-kit:  
   ```shell
   $ cd package/security/optee-os-dev-kit/dev_kit/
   $ cp -rp arm-plat-sun50iw1p1 arm-plat-sun50iw9p1
   ```

#### 1.2.2 Modifications for `optee-efuse-read` Demo

Add debug prints for keybox buffer:  
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
> `dump` is a built-in hex print function.  
> `128` corresponds to the key length.

#### 1.2.3 Allwinner-Specific APIs

- **`utee_sunxi_keybox`**  
  ```c
  TEE_Result utee_sunxi_keybox(const char *keyname, uint8_t *out_buf, uint32_t size);
  ```
  **Purpose**: Read keybox data by name.  
  **Parameters**:  
  - `keyname`: Key name (must match entries in `keybox_list`).  
  - `out_buf`: Output buffer (size ≥ `size`).  
  - `size`: Data length to read.  
  **Return**: `0` (success), `-1` (failure).  

- **`utee_sunxi_read_efuse`**  
  ```c
  TEE_Result utee_sunxi_read_efuse(const char *keyname, uint8_t *result_len, uint8_t *rd_buf);
  ```
  **Purpose**: Read efuse data.  
  **Parameters**:  
  - `keyname`: Key name.  
  - `result_len`: Output data length.  
  - `rd_buf`: Output buffer.  
  **Return**: `0` (success), others (failure).  

- **`utee_sunxi_write_efuse`**  
  ```c
  TEE_Result utee_sunxi_write_efuse(const char* keyname, uint8_t write_len, uint8_t *wr_buf);
  ```
  **Purpose**: Burn data to efuse.  
  **Parameters**:  
  - `keyname`: Key name.  
  - `write_len`: Data length (bytes).  
  - `wr_buf`: Data buffer.  
  **Return**: `0` (success), others (failure).  

---

### 1.3 Compile Secure Firmware

1. Enable `burn_key` in `sys_config.fex`:  
   ```
   [target]
   burn_key = 1
   ```
   ![image-20241122101659346](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241122101659346.png)  

2. Build firmware:  
   ```shell
   $ source build/envsetup.sh
   $ lunch apollo_p2-userdebug
   $ pack -sv
   ```
   > Output: `longan/out/h618_android12_p2_uart0_secure_secure_v0.img`  
   > (`v0` is the default version number).  

---

### 1.4 Burn Verification

1. Check `keybox_list` in U-Boot:  
   ```
   => printenv
   keybox_list=hdcpkey,widevine,xie
   ```

2. Verify TA/CA functionality:  
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

---

## 2. Burn Keybox

Use the **DragonSN** tool to burn keyboxes via USB.  

1. Open DragonSN:  
   ![image-20250318152318389](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152318389.png)  

2. Configure key settings:  
   - Select **Key Configuration**:  
     ![image-20250514092816895](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514092816895.png)  
   
   

   ![image-20250514093013109](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514093013109.png)
   
   
   
   - Add key entry (`xie` in this example):  
     ![image-20250318152922597](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318152922597.png)  
   
3. Select key file and burn options:  
   - Enable **Erase Before Burn** and **Power Off After Burn** (for production).  
   - Select key file:  
     ![image-20250318153520969](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318153520969.png)  

4. Start burning:  
   ![image-20250318154134143](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154134143.png)  

5. Completion:  
   ![image-20250318154206857](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250318154206857.png)  

---

## 3. Read Keybox

1. **U-Boot Verification**:  
   ```
   => pst read
   [259.435]name in map xie
   [259.450]1 data:
   48 39 25 17 00 00 00 00 78 69 65 00 00 00 00 00 
   ... (truncated)
   ```

2. **System-Level Read**:  
   ```shell
   # tee-supplicant &
   # efuse_read_demo_na -ext_key xie
   ```
   Output:  
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
   ... (truncated)
   ```
   > Compare the output with your burned key for verification.