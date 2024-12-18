# 21-LRADC key configuration

[TOC]

## LRADC

Location: Development board 20Pin [Expansion pin](expansion pin) No. 1 pin

* Low Rate ADC (LRADC)
* 6-Bit resolution
* Sampling rate up to 2 kHz
* Support for hold keys and universal keys
* Supports normal, continuous, and single working modes
* Power supply voltage: 1.8 V, power reference voltage: 1.35 V, analog input, detection voltage range: 0~ LEVELB (maximum 1.266 V)



## Hardware considerations

​		K5 LRADC is currently a SOC direct pin, and the ADC circuit design requires a pull-up voltage, and the maximum connection to LRADC does not exceed 1.266 V.



## dts

The device tree configuration for the LRADC module is located in longan's kernel directory `longan/device/config/chips/a133/configs/c3/board.dts`

The Linux path is `device/config/chips/a133/configs/c3/board.dts`

```c
sunxi_keyboard: keyboard {
    compatible = "allwinner,keyboard_1350mv";
	status = "okay"; /* Enable this node */
	key_cnt = <5>; /* Number of keys */
	keyo = <115 115>; /* According to the actual situation, the left 115 is the voltage, the unit is mV, and the right 115 is the key value corresponding to the voltage */
	keyl = <235 114>;
	key2 = <330 139>;
	key3 = <420 28>;
	key4 = <520 102>;
};
```



Key values can be referred to:

`longan/kernel/linux-4.9/include/dt-bindings/input/linux-event-codes.h`

```c
#define EV_KEY                  0x01
#define KEY_RESERVED            0
#define KEY_ESC                 1
#define KEY_1                   2
#define KEY_2                   3
#define KEY_3                   4
#define KEY_4                   5
#define KEY_5                   6
#define KEY_6                   7
#define KEY_7                   8
#define KEY_8                   9
#define KEY_9                   10
...
```



## debugging

### proc

Confirm to view LRADC's data reporting nodes

```
console:/ # cat /proc/bus/input/devices
I: Bus=0019 Vendor=0001 Product=0001 Version=0100
N: Name="sunxi-keyboard"
P: Phys=sunxikbd/input0
S: Sysfs=/devices/virtual/input/input0
U: Uniq=
H: Handlers=event0
B: PROP=0
B: EV=3
B: KEY=c000000000000 0
```



### getevent

Check if linux reports input events

```
getevent
getevent -l
```



