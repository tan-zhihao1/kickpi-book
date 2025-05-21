
# 02-Buildroot_System_Customization  

## Boot Logo  
1. Mount `/dev/mmcblk0p1`.  
2. Replace the `bootlogo.bmp` file.  

## Startup Programs  

**Disable Default QT Program Autostart**:  
```shell  
# On the board:  
mv etc/init.d/S90deviceTest_QT etc/init.d/D90deviceTest_QT  

# SDK path:  
buildroot/config/buildroot/allwinner/system/busybox-init-base-files/etc/init.d/S90deviceTest_QT  
```

**Add Custom Startup Program**:  
1. Create a script in `/etc/init.d/` on the board.  
2. SDK reference path:  
```shell  
buildroot/config/buildroot/allwinner/system/busybox-init-base-files/etc/init.d/  
```

## Adding Applications  
Buildroot does not support `apt`. Modify the SDK configuration to add or remove tools.  

**Add/Remove Tools via Buildroot**:  
1. Open the graphical configuration:  
```shell  
./build.sh buildroot_menuconfig  
```
2. Save changes:  
```shell  
./build.sh buildroot_saveconfig  
```
3. Recompile:  
```shell  
./build.sh && ./build.sh pack  
```

**Common Issues**:  
If changes do not take effect, run:  
```shell  
rm -rf out/t113/evb1_auto/buildroot/buildroot/target  
find out/t113/evb1_auto/buildroot/buildroot -name ".stamp_target_installed" | xargs rm -rf  
./build.sh lunch  
./build.sh && ./build.sh pack  
```

**Add User Applications or Tools**:  

**Method 1**:  
After a full SDK compilation, modify:  
```shell  
t113-linux/out/t113/evb1_auto/buildroot/buildroot/target  
```
Repack and recompile.  

**Method 2**:  
Modify the skeleton directory before compilation:  
```shell  
t113-linux/buildroot/buildroot-201902/system/skeleton  
```
If ineffective, use the cleanup commands above and recompile.  
[file content end]