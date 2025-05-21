# 04-Expansion_Pins_Testing


K4B Extension Pins  

![image-20250520102455370](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520102455370.png)

## Serial Ports

**Check device nodes**  
```shell
ls /dev/ttyS*
/dev/ttyS1  /dev/ttyS3  /dev/ttyS4  /dev/ttyS5
```

> **Serial port device nodes explanation**:  
> - TTL signals are on the expansion pins; RS485/232 signals are on the white terminals.  
>   - `ttyS1` ↔ UART1 (RS485)  
>   - `ttyS5` ↔ UART5 (RS232)  
>   - `ttyS3` ↔ UART3 (Debug port)  
>   - `ttyS4` ↔ UART4 (TTL)  

**Set baud rate**  
```shell
stty -F /dev/ttyS4 ispeed 115200 ospeed 115200 cs8
```

**Send data via serial port**  
```shell
echo kickpi > /dev/ttyS4
```

**Receive data**  
```shell
cat /dev/ttyS4
```

## GPIO  

**sys LED Control**  
GPIO pins can be registered as LEDs for direct control via the `brightness` node.  

**Check registered LEDs**  
```shell
$ ls /sys/class/leds/
PD14      PE11      PG0       PG12      PG13      PG14      PG15      work-led
```

**Example: Control PD14 pin**  
```shell
console:/ # ls /sys/class/leds/PD14
brightness device max_brightness power subsystem trigger uevent
console:/ # cat /sys/class/leds/PD14/brightness
255
console:/ # echo 0 > /sys/class/leds/PD14/brightness  # Set low level
console:/ # echo 1 > /sys/class/leds/PD14/brightness  # Set high level
```

**Sunxi GPIO Control**  
```shell
cd /sys/kernel/debug/sunxi_pinctrl

# View pin configuration
echo PH8 > sunxi_pin
cat sunxi_pin_configure

# Configure pin as GPIO output
echo 'PH8 1' > function

# Check pin level
cat data
echo 'PH8 1' > data  # Output high
echo 'PH8 0' > data  # Output low

# For PL and later pins, switch device name first:
echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
```

## PWM  
```shell
echo 3 > /sys/class/pwm/pwmchip0/export
echo normal > /sys/class/pwm/pwmchip0/pwm3/polarity  # or "inversed"
echo 100000 > /sys/class/pwm/pwmchip0/pwm3/period    # Period in ns
echo 20000 > /sys/class/pwm/pwmchip0/pwm3/duty_cycle # Duty cycle = duty_cycle/period
echo 1 > /sys/class/pwm/pwmchip0/pwm3/enable
```

**Output waveforms**  
- Normal polarity:  
![image-20250520140037541](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520140037541.png)  
- Inversed polarity:  
![image-20250520140046724](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520140046724.png)  

## SPI  
**Test command**  
```shell
spi_test -p "hello" -v
```
**Hardware setup**: Short-circuit PIN19 and PIN21.  
![image-20250520140416955](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520140416955.png)  

## I2C  
Allwinner TWI bus = I2C bus.  
```shell
dmesg -n1  # Reduce log level to avoid interference
i2cdetect -y 3  # Scan devices on bus 3 (e.g., device at 0x51)
```
![image-20250520142914945](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250520142914945.png)  

## USB  
**Supported pins**: PIN9, PIN11, PIN13, PIN15 (for USB devices).
