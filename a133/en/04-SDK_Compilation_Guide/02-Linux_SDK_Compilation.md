# 02-Linux_SDK_Compilation

### Full Compilation

```shell
$ ./build.sh config
Welcome to mkscript setup progress
All available platforms:
   0. android
   1. linux
Choice [android]: 1
All available linux_dev:
   0. bsp
   1. dragonboard
Choice [bsp]: 0
All available kern_ver:
   0. linux-4.9
Choice [linux-4.9]: 0
All available ic:
   0. a133
Choice [a133]: 0
All available boards:
   0. b1
   1. b3
   2. c3
   3. c4
   4. fpga
   5. perf1
   6. perf2
   7. perf3
   8. qa
   9. ver
Choice [c3]: 2
All available flash options:
   0. default
   1. nor
Choice [default]: 0
```

```shell
./build.sh lunch  # Select K5 or K5C
./build.sh       # Compile full image
```

---

### Partial Compilation

**Kernel Compilation**  
```shell
./build.sh kernel
```

**RootFS Compilation**  
```shell
./build.sh rootfs
```

---

## Kernel Defconfig Configuration

**Modify and Save Defconfig**:  
```shell
# Ensure at least one compilation or run: ./build.sh config
cd kernel/linux-4.9/
make ARCH=arm64 menuconfig  # Configure kernel
cd -
./build.sh saveconfig      # Save configuration
```

---

## Toolchain Paths

**Toolchain Package Path**:  
```
build/toolchain/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu.tar.xz
```

**Compiled Toolchain Path**:  
```
out/gcc-linaro-5.3.1-2016.05-x86_64_aarch64-linux-gnu
```

---

### Troubleshooting Missing Files

If encountering errors like:  
```
aarch64-linux-gnu-gcc: error: /home/A/sdk/a133/a133-linux-test1/kernel/linux-4.9/modules/gpu/img-rgx/linux/rogue_km/binary_sunxi_linux_release/target_aarch64/kbuild/services/server/env/linux/event.c: No such file or directory
```

**Solution**: Clean the build environment:  
```shell
$ ./build.sh clean
```