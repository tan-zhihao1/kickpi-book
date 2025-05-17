# 04-Expansion Pins

## K5 Expansion Pins  

![image-20241009150239258](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20241009150239258.png)  

## K5Ck Expansion Pins  

![image-20250515144550428](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/image-20250515144550428.png)  

* Pins marked with `*` indicate default software configurations.  
* **UART0_RX/TX**: Default debug UART.  
* **TWI** refers to I2C (e.g., `TWI1_SCK/SDA` = `I2C_CLK/SDA`).  

---

## UART  

**Check device nodes**  
```bash  
$ ls dev/ttyS*  
```
- `ttyS0` ↔ DEBUG_UART0  
- `ttyS2` ↔ UART2  
- `ttyS7` ↔ S_UART  

**Set baud rate**  
```bash  
stty -F /dev/ttyS2 ispeed 115200 ospeed 115200 cs8  
```

**Send data to UART**  
```bash  
echo kickpi > /dev/ttyAS2  
```

**Receive data**  
```bash  
cat /dev/ttyAS2  
```

---

## GPIO  

### sys LED Control  
LEDs registered as GPIO can be controlled via the `brightness` node.  

**High-level voltage**: PB-3.3V / PC-1.8V  

**List registered LEDs**  
```bash  
$ ls /sys/class/leds/  
    PC12  PC2  PC3  PC4  PC7  PH8  PL4  PL5  fan_en  spk_en  sysled  
```

**Example: Control PH8 pin**  
```bash  
console:/ # ls /sys/class/leds/PH8  
brightness device max_brightness power subsystem trigger uevent  

console:/ # cat /sys/class/leds/PH8/brightness  
255  

console:/ # echo 0 > /sys/class/leds/PH8/brightness  # Set low level  
console:/ # cat /sys/class/leds/PH8/brightness  
0  

console:/ # echo 1 > /sys/class/leds/PH8/brightness  # Set high level  
console:/ # cat /sys/class/leds/PH8/brightness  
1  
```

---

### sunxi GPIO Control  

```bash  
cd /sys/kernel/debug/sunxi_pinctrl  

# Check pin configuration  
echo PH8 > sunxi_pin  
cat sunxi_pin_configure  

# Configure pin as GPIO output  
echo 'PH8 1' > function  

# Verify configuration  
cat sunxi_pin_configure  

# Check pin level  
cat data  

# Set high level  
echo 'PH8 1' > data  

# Set low level  
echo 'PH8 0' > data  

# Note: For pins PL and beyond, switch pin device first  
echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name  
cat /sys/kernel/debug/sunxi_pinctrl/dev_name  

echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name  
cat /sys/kernel/debug/sunxi_pinctrl/dev_name  
```
