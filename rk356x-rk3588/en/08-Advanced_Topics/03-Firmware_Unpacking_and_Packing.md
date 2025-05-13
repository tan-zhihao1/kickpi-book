# 03-Firmware_Unpacking_and_Packing

This section mainly introduces the process of unpacking and repacking RK firmware on Windows or Linux.

## Windows

To be tested and improved.

## Linux

### Obtaining the tools
1. Network disk path:
```
rk356x_data\3-SoftwareData Software Materials\Linux_Pack_Firmware.zip
```
2. Linux-SDK source code path:
```
rk356x-linux\tools\linux\Linux_Pack_Firmware
```
3. Android-SDK source code path:
```
rk-android13.0\RKTools\linux\Linux_Pack_Firmware
```

### Unpacking
1. Place the `Linux_Pack_Firmware` directory in your virtual machine's working directory.
```shell
$ ls Linux_Pack_Firmware/
rockdev
```
2. Put the firmware to be unpacked in the `rockdev` directory and rename it to `update.img`.
```shell
$ cd Linux_Pack_Firmware/rockdev
$ mv update-rk3568-kickpi-k1-linux-debian--20250403-150845.img update.img
```
3. Run the script to unpack the firmware.
```shell
$ ./unpack.sh
```
4. The unpacked partition images and other files are located in:
```shell
$ ls output/
Image  MiniLoaderAll.bin  package-file  parameter.txt
```
> After modifying or replacing the unpacked partition images, you can repack them to generate a new firmware.

### Packing
After modifying the unpacked images, you can repack them.
1. Copy the files to the corresponding paths in the `rockdev` directory and delete the `update.img` used for unpacking.
```shell
$ cp -rf output/* ./ 
$ mv parameter.txt Image/
$ mv MiniLoaderAll.bin Image/
$ rm update.img
```
2. Modify the `package-file` to update the file paths to the actual locations of the files.
```
# NAME  PATH
package-file    package-file
parameter       Image/parameter.txt
bootloader      Image/MiniLoaderAll.bin
uboot   Image/uboot.img
misc    Image/misc.img
boot    Image/boot.img
recovery        Image/recovery.img
backup  RESERVED
rootfs  Image/rootfs.img
```
3. Run the corresponding version script to pack the firmware.
```shell
$ ./rk356x-mkupdate.sh
```
4. The packed image is located in the `rockdev` directory:
```shell
$ ls update.img
```