# 03-Android_SDK_Compilation

### SDK Overview

**Android SDK Version Information**  
- Kernel Version: 4.9  
- Android OS Version: 10.0  

---

## Android SDK Compilation

### Full Image Compilation

```shell
$ cd longan/
$ ./build.sh config

Welcome to mkscript setup progress
All available platforms:
   0. android
   1. linux
Choice [android]: 0
All available ICs:
   0. a133
   1. t509
Choice [a133]: 0
All available boards:
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

```shell
$ cd -
$ ./build.sh lunch  # Select K5 or K5C based on board type
$ ./build.sh
```

> **Output Directory**: `longan/out/`

---

### Defconfig Configuration

**Defconfig Path**:  
```
longan/kernel/linux-4.9/arch/arm64/configs/sun50iw10p1smp_a133_android_defconfig
```

**Configuration Commands**:  
```shell
cd longan

# Operations: loadconfig | menuconfig | saveconfig | mergeconfig
./build.sh loadconfig    # Load default configuration
./build.sh menuconfig    # Open interactive menu
./build.sh saveconfig    # Save changes
./build.sh mergeconfig   # Merge configurations
```

---

### Fixing `yylloc` Compilation Errors

Due to differences in compilation environments (e.g., `ld` versions), `yylloc` errors may occur. Modify the code as follows based on the error path:

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

> **Note**: Adjust the file path according to the actual error message.  

```