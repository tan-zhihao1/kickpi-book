# 22 - Kernel driver customization

## configure defconfig

Android kernel configuration

```
$./build.sh -M
```

Linux kernel configuration

```
$./build.sh kernel-config
```

kernel path

```
Kernel-5.10
```

kernel config command common configuration

```
CD kernel-5.10/
make ARCH=arm64 menuconfig//According to the IC architecture, the current rk356x/3588 uses arm64.
//main command
make loadconfig			//restore old
make menuconfig			//Enter the configuration graphical interface
make saveconfig			//save as default configuration
make ARCH=arm64 rockchip_defconfig	//load defconfig in this path
```

> Configuration can be tracked by build.sh

Make saveconfig generate defconfig requires cp to be compiled into the * defconfig file used by the SDK for use

```
cp defconfig arch/arm64/configs/rockchip_defconfig
```

> Different versions of defconfig may be different, you can find the corresponding version file in the git history.
>
> $ git log --name-only   kernel-5.10/ | grep defconfig



## dts

rk356x-linux

```
kernel/arch/arm64/boot/dts/rockchip/
	rk3562-kickpi-k3-linux.dts
	rk3568-kickpi-k1-linux.dts
	rk3568-kickpi-k1b-linux.dts
	rk3588-kickpi-k8-linux.dts
```

rk-android

```
kernel-5.10/arch/arm64/boot/dts/rockchip
 rk3568-kickpi-k1-android.dts
 rk3568-kickpi-k1b-android.dts
 rk3562-kickpi-k3-android.dts
 rk3588-kickpi-k8-android.dts
```



### sys gpio control

When there is a need to configure the extension pin as an input, the default software's GPIO-LED does not meet the requirement.

Release the GPIO and control it through /sys/class/gpio

**PIN PIN Calculation **

Take GPIO1-D0 (gpio1-24) as an example:

```
Each GPIO has 32 bits: 0-32
A (0-7) B (8-15) C (16-23) D (24-31)
GPIO1-D0 calculation Pin num = 32 * 1 + 24 = 56
```



**Step 1**

Note the corresponding GPIO pins first, '/sys/class/gpio/export' can only import unregistered gpio

Unenable the device tree corresponding IO

The device tree is located at:

```
kernel-5.10/arch/arm64/boot/dts/rockchip/
40pin:	rk3568-kickpi-extend-40pin.dtsi
20pin:	rk3568-kickpi-extend-20pin.dtsi
```

**Step 2**

Compilation mirroring, reburning

**Step three**

Confirm that gpio is not registered

```
cat /sys/kernel/debug/pinctrl/pinctrl-rockchip-pinctrl/pinmux-pins
```

> Unregistered as follows:pin 56 (gpio1-24): (MUX UNCLAIMED) (GPIO UNCLAIMED)

**Step four**

Register with /sys/class/gpio/export 56 and take control
Register
```
root@kickpi:~# echo  56 > /sys/class/gpio/export
```
Check if it is generated
```
root@kickpi:~# ls /sys/class/gpio/
export  gpio56  gpiochip0  gpiochip352  unexport
```
Node content after registration
```
root@kickpi:~# ls /sys/class/gpio/gpio56
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

Control gpio through the content under the node, commonly used as follows

```
direction
	in / out
	echo in > /sys/class/gpio/gpio56/direction
	echo out > /sys/class/gpio/gpio56/direction
value
	0 / 1
	cat /sys/class/gpio/gpio56/value 		// read
	echo 1 > /sys/class/gpio/gpio56/value	// Configure High
	echo 0 > /sys/class/gpio/gpio56/value  // Configure low
```





## PWM configuration

For example, configure the GPIO on the K1 extension pin to PWM.





