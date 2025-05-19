# 04-Expansion_Pins

## Expansion Pins <a id="ExpansionPin"> </a>

### K1 Pin List

![K1_40PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/引脚定議_画板 1 副本.jpg)

### K1B Pin List

![K1B_20PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/引脚定議_画板 1 副本 2.jpg)

### K3 Pin List

![K3_20PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/引脚定議_画板 1 副本 5.jpg)

### K8 Pin List

![K8_40PIN](http://tanzhtanzh.oss-cn-shenzhen.aliyuncs.com/img/引脚定議-10.jpg)

## GPIO

Multiple controllable GPIOs are configured by default in the expansion pins of the development board.

First, confirm the location and number of the GPIO pins through the [Expansion Pins](#ExpansionPin) section. You can control the state of the specified GPIO by referring to the following steps.

### User-level Control of GPIO Level

> GPIOs configured in the default software expansion pins are generally registered for this type of control.
>
> This method can control the pin to output a high or low level.

**View the GPIO registration list**

```
$ ls /sys/class/leds/

gpio1b0/ gpio1b2/ gpio1d4/ gpio3b6/ 
gpio1a4/ gpio1b1/ gpio1d0/ gpio3b5/ gpio4c4/
```

**Control GPIO via the command line**

```
$ echo 1 > /sys/class/leds/gpio3b6/brightness
$ echo 0 > /sys/class/leds/gpio3b6/brightness
```

## UART <a id='UART'> </a>

One or more UARTs are generally configured by default in the expansion pins of each series of boards. After connecting the corresponding UART to be used, you can perform tests as follows.

* Confirm the serial port number to be used

Check if the corresponding node exists

```shell
$ ls /dev/ttyS*
```

> For example, UART4 corresponds to /dev/ttyS4

Perform a communication test using microcom

> Android has microcom installed by default.

```shell
$ microcom -s 115200 /dev/ttyS4
```

> If the node found for serial port 4 is /dev/ttyS4
>
> -s sets the baud rate to 115200

Verify using minicom:

> It is recommended to use minicom on Linux.

**Configuration is required for the first installation:** 

* Use `minicom -s` to turn off `Serial port setup >> F Hardware Flow Control`.
* Press the Esc key or Enter to return to the previous level and select `Save setup as dfl` to save.
* Select `Exit from Minicom` to exit.

Use the following command:

```shell
$ minicom -b 9600 -D /dev/ttyS3
```

> Default display: The received content will be printed, and the sent content will not be printed.
>
> Press `Ctrl+A` to enter the control mode for commands from B to Z. Press `Z` for help.

> | Ctrl+A X | Exit the program                        |
> | -------- | --------------------------------------- |
> | Ctrl+A W | Enable/disable auto line feed (disabled by default) |
> | Ctrl+A E | **Enable/disable input display** (disabled by default) |
> | Ctrl+A C | Clear the screen                        |

Alternatively, use `echo` to send data directly

```shell
$ stty -F /dev/ttyAS3 -a 	// View the default baud rate
$ stty -F /dev/ttyAS3 115200 	// Set the baud rate
$ echo "123" > /dev/ttyAS3
```

## PWM <a id="PWM"> </a>

The RK3568 has 4 PWM modules, and each PWM module has 4 channels of PWM, totaling 16 channels of PWM.

### User-level Configuration of PWM Output

The Extend 40Pin interface includes multiple channels of PWM. Taking the PWM3 channel as an example, enter the PWM configuration according to the following commands.

After successfully setting the configuration parameters as shown in the example, you can use a multimeter to measure the PWM3 pin, and the correct voltage should be around 1.6V.

Example: Set the PWM3 channel with a period of 10000ns, a duty cycle of 5000ns, and a polarity of normal.

The PWM path is `/sys/class/pwm`, and `pwmchip*` is numbered starting from 0 according to the enabling order. The following shows the settings for `pwmchip0`:

```
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```

* How to confirm the PWM corresponding to `pwmchip`

```
$ find /sys/devices/ | grep pwmchip | grep uevent
/sys/devices/platform/fd8b0020.pwm/pwm/pwmchip1/uevent
/sys/devices/platform/fd8b0010.pwm/pwm/pwmchip0/uevent
/sys/devices/platform/fd8b0030.pwm/pwm/pwmchip2/uevent
```

> `fd8b0010.pwm` is the corresponding node in the DTS.

## ADC

The expansion pins of the mainboard are equipped with ADC. You can view the corresponding ADC position and channel number through the [Expansion Pins section](#ExpansionPin).

The following uses `SARADC_VIN3` as an example for illustration.

**Read the ADC value**

```
$ cat /sys/bus/iio/devices/iio\:device0/in_voltage*_raw
```

Example:

Read the voltage value of channel 3

```
#  cat /sys/bus/iio/devices/iio\:device0/in_voltage3_raw
3528
```

## CAN

Applicable platforms: RK3568 / RK3576 / RK3588

Some RK chips support native CAN interfaces, which are usually led out to the expansion pins. The specific location can be found in the expansion pins section.

**The native CAN interface of the chip cannot be used directly; an external level conversion circuit needs to be added.**

**Query the current network devices**

```
$ ifconfig -a
```

**CANFD Configuration**

Turn off the CAN

```
$ ip link set can0 down
```

Set the bit rate to 500KHz

```
$ ip link set can0 type can bitrate 500000
```

Print the information of `can0`

```
$ ip -details -statistics link show can0
```

Turn on the CAN

```
$ ip link set can0 up
```

**CAN FD Transmission**

Transmit (standard frame, data frame, ID: 123, data: DEADBEEF):

```
$ cansend can0 123#DEADBEEF
```

Transmit (standard frame, remote frame, ID: 123)

```
$ cansend can0 123#R
```

**CAN Reception**

Turn on printing and wait for reception

```
$ candump can0
```

**Loopback Mode Test**

Method 1:

After turning on the CAN, enter the following command to start the loopback self-test (the base address is configured according to the actual CAN started in the DTS).

```
$ io -4 0xfea60000 0x8415
```

> `0xfea60000` is the DTS node of K8 CAN0.

In loopback mode, `candump` can receive data after `cansend`.

**Example**

- Terminal 1

Start the loopback mode test and monitor the CAN

```
root@linaro-alip:/# io -4 0xfea60000 0x8415
root@linaro-alip:/# candump can0
  can0  123   [4]  DE AD BE EF
```

- Terminal 2

Send the command

```
root@linaro-alip:/# cansend can0 123#DEADBEEF
```

Method 2:

Start the loopback test

```shell
$ ip link set can0 type can loopback on
```

Run `candump` in the background and turn on printing

```shell
$ candump can0 &
```

Send the command

```shell
$ cansend can0 123#DEADBEEF
```

## SPI

**Tool Location**

```
$ kernel/tools/spi/spidev_test.c
```

**Compilation**

```
$ make CROSS_COMPILE=~/path-to-toolchain/gcc-xxxxx-toolchain/bin/xxxx-linux-gnu-
```

> Select the `CROSS_COMPILE` used by the kernel.

**Loopback Mode Test**

```
$ spidev_test -D /dev/spidev0.0 -v -l -p "hello"
```

> The main controller acts as the Master.
>
> Short-circuit the MISO and MOSI pins.

- Example

```
root@linaro-alip:/data# ./spidev_test -D /dev/spidev0.0 -v -l -p "hello"
spi mode: 0x24
bits per word: 8
max speed: 500000 Hz (500 kHz)
TX | 68 65 6C 6C 6F __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __  |hello|
RX | 68 65 6C 6C 6F __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __ __  |hello|
```