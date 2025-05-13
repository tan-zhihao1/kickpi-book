# GPIO

Common GPIO configurations are typically set as LED outputs or key inputs. The following are examples of modifying this part of the device tree for reference.

Note: Any IO can only be configured with one function. If you need to modify an already used IO, you need to find the corresponding position and comment out the original usage.

## LED Configuration

For example, to configure GPIO1_D4 on the K1 expansion pin as an LED, you can refer to the following modifications.

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
@@ -29,10 +29,10 @@ gpio1d0 {
         gpios = <&gpio1 RK_PD0 GPIO_ACTIVE_HIGH>;
         default-state = "off";
     };
+    gpio1d4 {
+        gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_HIGH>;
+        default-state = "off";
+    };
     gpio3b5 {
         gpios = <&gpio3 RK_PB5 GPIO_ACTIVE_HIGH>;
         default-state = "off";

```

**Testing**

* View the GPIO registration list

```
$ ls /sys/class/leds/

gpio1b0/ gpio1b2/ gpio1d4/ gpio3b6/ 
gpio1a4/ gpio1b1/ gpio1d0/ gpio3b5/ gpio4c4/
```

* Control the GPIO from the command line

```
$ echo 1 > /sys/class/leds/gpio1d4/brightness
$ echo 0 > /sys/class/leds/gpio1d4/brightness
```

## gpio-key Configuration

For example, to configure GPIO1_D4 on the K1 expansion pin as KEY_1, you can refer to the following modifications.

```diff
--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-extend-40pin.dtsi
@@ -29,10 +29,10 @@ gpio1d0 {
         gpios = <&gpio1 RK_PD0 GPIO_ACTIVE_HIGH>;
         default-state = "off";
     };
-    gpio1d4 {
-        gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_HIGH>;
-        default-state = "off";
-    };
+    // gpio1d4 {
+    //     gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_HIGH>;
+    //     default-state = "off";
+    // };
     gpio3b5 {
         gpios = <&gpio3 RK_PB5 GPIO_ACTIVE_HIGH>;
         default-state = "off";

--- a/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi
+++ b/kernel/arch/arm64/boot/dts/rockchip/rk3568-kickpi-k1.dtsi
@@ -76,6 +76,21 @@ fan0: gpio-fan {
                status = "okay";
        };
        */
+
+       
+       gpio-keys {
+               compatible = "gpio-keys";
+               autorepeat;
+           status = "okay";
+       
+               key_1D4 {
+                       label = "GPIO_KEY_1D4";
+                       linux,code = <KEY_1>;
+                       gpios = <&gpio1 RK_PD4 GPIO_ACTIVE_LOW>;
+                       debounce-interval = <100>;
+               };
+       };
+
 };
 
 &leds {
(END)
```

The code values can be referred to in the macro definitions in the driver.

```
rk356x-linux\kernel\include\uapi\linux\input-event-codes.h
```

**Testing**

* Use the command `getevent` for Android testing:

```
console:/ # getevent
add device 1: /dev/input/event3
  name:     "sunxi-ir-uinput"
add device 2: /dev/input/event2
  name:     "gt9xxnew_ts"
add device 3: /dev/input/event0
  name:     "sunxi-ir"
add device 4: /dev/input/event1
  name:     "gpio-keys"
/dev/input/event2: 0001 014a 00000001
/dev/input/event2: 0003 0035 00000247
/dev/input/event2: 0003 0036 000001c3
/dev/input/event2: 0003 0030 0000001e
/dev/input/event2: 0003 0032 0000001e
```

* Use `evtest` for Linux testing:

```
root@ubuntu2004:~# evtest 
No device specified, trying to scan all of /dev/input/event*
Available devices:
/dev/input/event0:      fe6e0030.pwm
/dev/input/event1:      rk805 pwrkey
/dev/input/event2:      rockchip-rk809 Headset
/dev/input/event3:      hdmi_cec_key
/dev/input/event4:      adc-keys
/dev/input/event5:      gpio-keys
Select the device event number [0-5]: 5
Input driver version is 1.0.1
Input device ID: bus 0x19 vendor 0x1 product 0x1 version 0x100
Input device name: "gpio-keys"
Supported events:
  Event type 0 (EV_SYN)
  Event type 1 (EV_KEY)
    Event code 2 (KEY_1)
Key repeat handling:
  Repeat type 20 (EV_REP)
    Repeat code 0 (REP_DELAY)
      Value    250
    Repeat code 1 (REP_PERIOD)
      Value     33
Properties:
Testing ... (interrupt to exit)
Event: time 1699275783.300685, type 1 (EV_KEY), code 2 (KEY_1), value 1
Event: time 1699275783.300685, -------------- SYN_REPORT ------------
Event: time 1699275783.573759, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.573759, -------------- SYN_REPORT ------------
Event: time 1699275783.610429, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.610429, -------------- SYN_REPORT ------------
Event: time 1699275783.647094, type 1 (EV_KEY), code 2 (KEY_1), value 2
Event: time 1699275783.647094, -------------- SYN_REPORT ------------
Event: time 1699275783.647094, type 1 (EV_KEY), code 2 (KEY_1), value 0
Event: time 1699275783.647094, -------------- SYN_REPORT ------------

```

## sys GPIO Control

When a GPIO is not in use, it can be controlled through `/sys/class/gpio`.

**PIN Calculation**

Take GPIO1-D0 (gpio1-24) as an example:

```
Each GPIO group has 32 bits: 0 - 32
A (0 - 7) B (8 - 15) C (16 - 23) D (24 - 31)
For GPIO1-D0, calculate the Pin num = 32 * 1 + 24 = 56
```

**Step 1: Ensure the GPIO is not in use**

First, comment out the corresponding GPIO pin. `/sys/class/gpio/export` can only import unregistered GPIOs. Disable the corresponding IO in the device tree.

**Step 2: Compile the image and re-flash**

**Step 3: Confirm the GPIO is not registered**

```
cat /sys/kernel/debug/pinctrl/pinctrl-rockchip-pinctrl/pinmux-pins
```

> If not registered, it will be as follows: pin 56 (gpio1-24): (MUX UNCLAIMED) (GPIO UNCLAIMED)

**Step 4: Control the IO port**

Register 56 through `/sys/class/gpio/export` and control it.

```
// Register
root@kickpi:~# echo  56 > /sys/class/gpio/export
// Check if it is generated
root@kickpi:~# ls /sys/class/gpio/
export  gpio56  gpiochip0  gpiochip352  unexport
// Contents of the node after registration
root@kickpi:~# ls /sys/class/gpio/gpio56
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

Control the GPIO through the contents under the node. Commonly used ones are as follows:

```
direction
	in / out
	echo in > /sys/class/gpio/gpio56/direction
	echo out > /sys/class/gpio/gpio56/direction
value
	0 / 1
	cat /sys/class/gpio/gpio56/value 		// Read
	echo 1 > /sys/class/gpio/gpio56/value	// Configure high level
	echo 0 > /sys/class/gpio/gpio56/value  // Configure low level
```
