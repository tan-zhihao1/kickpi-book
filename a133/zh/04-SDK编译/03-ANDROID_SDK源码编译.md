# 03-ANDROID_SDK源码编译

## SDK介绍

Android SDK 版本信息

内核版本：4.9

Android系统版本：10.0



## Android SDK编译

### 编译完整镜像

**环境配置**

```
$ cd longan/
$ ./build.sh config

Welcome to mkscript setup progress
All available platform:
   0. android
   1. linux
Choice [android]: 0
All available ic:
   0. a133
   1. t509
Choice [a133]: 0
All available board:
   0. b1
   1. b3
   2. b4
   3. c3
   4. c4
   5. dpf
   6. fpga
   7. perf1
   8. perf2
   9. perf3
  10. qa
  11. ver
Choice [c3]: 3
```

**编译整包镜像**

```
$ cd - 
$ ./build.sh lunch //根据板型选择K5或K5C
$ ./build.sh
```

> 镜像生成目录：longan/out/



### **kconfig **

**路径**

```
longan/kernel/linux-4.9/arch/arm64/configs/sun50iw10p1smp_a133_android_defconfig
```

**配置kconfig**

```
$ cd longan
$./build.sh loadconfig
$./build.sh menuconfig
$./build.sh saveconfig
$./build.sh mergeconfig
```

### 设备树路径

``` 
a133-android10.0-v2.0/longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts

```



## 常见问题

**由于编译环境 ld 不同，可能会导致 yylloc 报错，需要修改代码中的yylloc**

实际路径根据报错提示修改

```diff
--- a/longan/kernel/linux-4.9/scripts/dtc/dtc-lexer.lex.c
+++ b/longan/kernel/linux-4.9/scripts/dtc/dtc-lexer.lex.c
@@ -631,8 +631,8 @@ char *yytext;
 #include "srcpos.h"
 #include "dtc-parser.tab.h"

-//YYLTYPE yylloc;
-extern YYLTYPE yylloc;
+YYLTYPE yylloc;
+//extern YYLTYPE yylloc;
```

