# 02-Linux_SDK_Compilation

### Obtaining the SDK Source Code

* Network Disk Download Directory:

```
1-SDK\Linux\
	h618-linux-xxxxxxx.tar.gz
```

* Copy the source code archive to the compilation host directory:

  > Notes:
  >
  > 1. Do not place the source code in a shared directory for compilation.
  > 2. Do not use the ROOT account to log in to the compilation host.

* Verify file integrity using MD5:

```
$ md5sum -c *.md5
```

* Extract the source code archive:

```
$ tar -zxvf *.tar.gz
```

> After extraction, only the `.git` folder will be visible in the source directory. Proceed to the next step to fully restore the source code.

* Restore the source code using Git:

```
$ git reset --hard
```

---

### Compilation Commands

Ensure the compilation environment has internet access. Required tools will be automatically downloaded during compilation.

Basic compilation:
```
$ ./build.sh
```

Compile firmware for creating a bootable SD card:
```
$ ./build.sh auto_burn=true
```

> To disable the auto-burn service after booting from the SD card:
```
$ sudo systemctl disable kickpi.service 
```

> To enable the auto-burn service (enabled by default):
```
$ sudo systemctl enable kickpi.service 
```

##### Interactive Build Interface

Run `./build.sh` in the `aw-image-build` root directory to enter the interactive build interface:

![image-20241025181130073](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181130073.png)

Options:
- **Build all step**: Full image build (automatically executes step1 to step4).
- **step1.Build Kernel**: Compile the kernel separately.
- **step2.Build U-boot**: Compile U-boot separately.
- **step3.Build base-rootfs and deb packages**: Build the root filesystem and package custom deb files.
- **step4.Pack image**: Package all components into a complete burnable image.
- **clean source/build/out files**: Clean source code, build files, and output images.

For a standard bootable image, select **Build all step** and press Enter.

![image-20250324091854498](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324091854498.png)

Select the target board (e.g., **kickpi-k2b Allwinner H618**):

![image-20241025181249872](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181249872.png)

Choose the root filesystem distribution and release (e.g., **jammy Ubuntu**):

![image-20241025181345456](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181345456.png)

Select the root filesystem type (e.g., **desktop**):

![image-20241025181414687](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181414687.png)

Choose the desktop environment (e.g., **Xfce**):

![image-20241025181456150](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181456150.png)

Select the desktop configuration profile (default: **base configuration**):

![image-20241025181509226](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025181509226.png)

Select pre-installed software packages using the spacebar, then press Enter to start the build. The output image will be saved in `out/images`.

---

### Single-Step Build

For customization (e.g., modifying the kernel or root filesystem), use single-step builds. Run `./build.sh` to access the interactive interface.

![image-20241025182302642](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241025182302642.png)

#### step1.Build Kernel

Compile the kernel and generate a deb package.

1. Select **step1.Build Kernel** and choose the target board (e.g., **kickpi-k2b Allwinner H618**).
2. After compilation, transfer the generated `.deb` file to the board and install it:
   ```
   $ dpkg -i linux-***.deb
   ```
   Reboot the board to apply the new kernel.

**Modifying Kernel Configuration**

1. Navigate to the kernel source directory (see **Compiler kernel path** from build logs):
   ```
   cd /home/jiawen/Allwinner/H618/aw-image-build/source/kernel/linux-5.4-h618 
   ```
2. Modify the configuration:
   ```
   make ARCH=arm64 menuconfig
   ```
3. Save and update the board's kernel configuration:
   ```
   make ARCH=arm64 savedefconfig
   cp defconfig arch/arm64/configs/linux_h618_defconfig
   git diff arch/arm64/configs/linux_h618_defconfig
   ```

---

#### step2.Build U-boot

Compile U-boot and package it into a deb file.

1. Select **step2.Build U-boot** and choose the target board.
2. Transfer the generated `.deb` to the board and install it:
   ```
   $ dpkg -i lubancat-a1-uboot-xxx.deb
   ```
3. Update the bootloader:
   ```
   $ sudo nand-sata-install
   ```
   Select **5 Install/Update the bootloader on SD/EMMC**, confirm warnings, and reboot.

---

### Compilation Toolchain

The compiled toolchain is located at:
```
aw-image-build/toolchains/gcc-linaro-7.4.1-2019.02-x86_64_arm-linux-gnueabi/bin
```