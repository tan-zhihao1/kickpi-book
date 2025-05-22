# 04-Expansion_Pins_Testing

## GPIO

### WiringKP Tool Usage

Check whether the system has WiringKP tools

``` 
which gpio
```

![image-20250514102207335](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514102207335.png)

If not, download and copy it from the network disk to the board system

Tool download path: 
`h618_data\3-SoftwareData\GPIO_tools`

Extract `wiringKP.tar.gz` on the board. Place executables and libraries into `/usr/bin` and `/usr/lib`.

**Example Usage**  

```shell
gpio readall // Get all pin status
```

![image-20250314150200725](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250314150200725.png)

```shell
gpio read <wPi>        // Read pin level
gpio mode <wPi> <mode> // Set pin mode (supports out/in/up/down/pwm)
gpio write <wPi> <val> // Set pin output level

pwm related command
gpio pwmr <wPi> <val>    //Setting ARR
gpio pwm <wPi> <val>     //Setting CCR
gpio pwmc <wPi> <val>    //Setting Frequency Dividing Coefficient
gpio pwmTone <wPi> <val> //Setting Frequency
```

**Set PH5 to output mode and output high level **

(Note: PC pins output 1.8V, PH pins output 3.3V):

```shell
kickpi@kickpi:~$ gpio mode 0 out
kickpi@kickpi:~$ gpio read 0
0
kickpi@kickpi:~$ gpio write 0 1
kickpi@kickpi:~$ gpio read 0
1
```

Use a multimeter to verify PH5 is high.  
**Set PH5 to input mode with pull-down**

```shell
kickpi@kickpi:~$ gpio mode 0 in
kickpi@kickpi:~$ gpio mode 0 down
kickpi@kickpi:~$ gpio read 0
0
kickpi@kickpi:~$ // Short pins 3 and 4
kickpi@kickpi:~$ gpio read 0
1
```

**Set PH2 to PWM mode**

``` shell
root@kickpi:~# gpio mode 3 pwm #Default output frequency 23475Hz Square wave with duty cycle 50%
```

![image-20250514115200103](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514115200103.png)

**Adjust duty cycle**

PWM duty cycle = CCR/ARR

CCR range: 0~65535 (default 512)

ARR range: 1~65536 (default 1024)

``` shell
gpio pwmr 3 2048 //Set ARR to 2048 Duty cycle to 512/2048=25%
```

![image-20250514115347443](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514115347443.png)

``` shell
gpio pwm 3 1024 //Set CCR to 1024 Duty cycle to 1024/2048=50%
```

![image-20250514115517926](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514115517926.png)

**Adjust frequency**

``` shell
gpio mode 3 pwm //The default output frequency is 23475Hz
//Set the frequency division coefficient to 5, then the output frequency is 23475/5=4695Hz, the actual frequency is 4688Hz, the error can be ignored
gpio pwmc 3 5   
```

![image-20250514115800211](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514115800211.png)

``` shell
gpio pwmTone 3 20000 //Directly set the frequency to 20000Hz
```

![image-20250514115920195](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250514115920195.png)

### 

### sys GPIO Control

**Step 1: Release GPIO via `/sys/class/gpio`**  
Ensure the target GPIO is in `UNCLAIMED` state:

```shell
cat /sys/kernel/debug/pinctrl/300b000.pinctrl/pinmux-pins
```

If pins are claimed (e.g., PH2/PH3 used as UART5), modify the device tree to disable default functions:

![image-20250326115428401](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250326115428401.png)

Edit device tree files:
```diff
vim longan/device/config/chips/h618/configs/p2/linux-5.4/board-*.dts  // Android
vim source/kernel/linux-5.4-h618/arch/arm64/boot/dts/sunxi/sun50iw9-kickpi-k2b.dts // Linux
```

![image-20250326133713399](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250326133713399.png)

**Step 2: Rebuild and flash the firmware.**  
**Step 3: Confirm GPIO is unclaimed:**

```shell
cat /sys/kernel/debug/pinctrl/300b000.pinctrl/pinmux-pins | grep PH
```

![image-20250326142700157](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250326142700157.png)

**Step 4: Export and control GPIO (e.g., PH2=226, PH3=227):**

![image-20250326142738724](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250326142738724.png)

Set direction:
```shell
echo in > /sys/class/gpio/gpio226/direction
echo out > /sys/class/gpio/gpio226/direction
```

Read/write value:
```shell
cat /sys/class/gpio/gpio226/value      // Read
echo 1 > /sys/class/gpio/gpio226/value // Set high
echo 0 > /sys/class/gpio/gpio226/value // Set low
```

---

### Example Configurations

**GPIO Output**  
Default GPIO outputs on K2B: PC12 (pin 12) and PC7 (pin 14).  
*Note: Other pins may require disabling default functions.*

Control methods: `gpio-leds` (Linux kernel driver) or `gpio_para` (Allwinner driver).

**gpio-leds Example (Heartbeat LED):**
```dts
/{ 
    gpio-leds {
        compatible = "gpio-leds";
        status = "okay";
        sys_led {
            label="sys_led";
            gpio = <&pio PI 16 GPIO_ACTIVE_HIGH>;
            linux,default-trigger = "heartbeat";
        };
    };
};
```

**gpio_para Example (PC7/PC12 Control):**
```dts
&soc {
    gpio_para {
        gpio_num = <2>;
        gpio_pin_1 = <&pio PC 7 GPIO_ACTIVE_HIGH>;
        gpio_pin_2 = <&pio PC 12 GPIO_ACTIVE_HIGH>;
        status = "okay";
    };
};
```

Control commands:
```shell
echo 1 > /sys/class/gpio_sw/PC12/data  // High
echo 0 > /sys/class/gpio_sw/PC12/data  // Low
```

**GPIO Key Example:**
```dts
/{ 
    gpio-keys {
        compatible = "gpio-keys";
        status = "okay";
        autorepeat;
        power {
            label = "Power Key";
            linux,code = <KEY_POWER>;
            gpio = <&pio PC 2 GPIO_ACTIVE_LOW>; // PC7
            wakeup-source;
            debounce-interval = <100>;
        };
    };
};
```

---

## Serial Port

**Test Tools**

Use USB-to-TTL cable.

![image-20241231145656021](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241231145656021.png)

**TTL Standard Serial Port**

Connect using a serial port tool.

![66637824ee48c91b31130503a4400149](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/66637824ee48c91b31130503a4400149.jpg)

**Software Testing Methods**

`ttyAS5` corresponds to serial port 5 (pins PH2 and PH3) with a baud rate of 115200.

```shell
stty -F /dev/ttyAS5 ispeed 115200 ospeed 115200 cs8 
```

Send data to the serial port:

```shell
echo kickpi > /dev/ttyAS5  
```

Receive data:

```shell
cat /dev/ttyAS5 
```





## Modify UART Control Port

Change `debug_uart` to an unused UART (e.g., UART0 is reserved for U-Boot logs):

```diff
+++ b/source/kernel/linux-5.4-h618/arch/arm64/boot/dts/sunxi/uEnv/uEnv.txt
@@ -4,4 +4,4 @@ console=both
 disp_mode=1080p60
 fb0_width=1920
 fb0_height=1080
-debug_uart=ttyAS0
+debug_uart=ttyAS3
```

---

## Add UART2 Support

PH6/PH7/PH8/PH9 default to SPI. PH4/PH5 default to I2C. To use PH5/PH6 as UART2:  
1. Disable default functions.  
2. Enable UART2 in the device tree.

![cf9c366bb88d7214bc93412cbc0accb3](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/cf9c366bb88d7214bc93412cbc0accb3.jpg)

---

## TTL-to-485 Module Wiring

Connect to UART5:  
![image-20250324195428239](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324195428239.png)

---

## TTL-to-232 Module Wiring

Connect to UART5:  
![image-20250324195442978](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250324195442978.png)