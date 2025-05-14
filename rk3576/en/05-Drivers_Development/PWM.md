# PWM

## Symbol Explanation

* `SDK$`: Refers to the source code path.

* `console$`: Generally refers to the command-line console of the motherboard. [Mainboard Command-line Console](..\02-Getting_Started_Guide\02-Quick_Start_Guide.md#console_readme)

* `ADB$`: Android Debug Bridge command-line tool, generally refers to an environment where ADB can be run.

The Extend 40Pin interface contains multiple channels of PWM. The specific pins are shown in the Expansion Pin Section.

The following uses PWM2_CH7_M2 as an example for explanation.

![image-20250421155643696](C:\Users\16708\AppData\Roaming\Typora\typora-user-images\image-20250421155643696.png)

For pwmX_Ych_Z, X represents the controller ID, Y represents the total number of channels supported by the current controller, and Z represents the channel ID.

PWM2_CH7_M2 represents:

> pwm0_2ch_1: pwm@27331000
>
> pwm2_8ch_6: pwm@2ade6000
>
> pwm2_8ch_7: pwm@2ade7000

List relevant PWM nodes:

```
$ ls /sys/class/pwm/
pwmchip0  pwmchip1  pwmchip2  pwmchip3
```

You can view the corresponding PWM DTS nodes:

```
$ cat /sys/class/pwm/pwmchip0/device/uevent | grep FULLNAME
OF_FULLNAME=/pwm@27331000
```

> It indicates that pwnchip0 corresponds to PWM0 Channel 1.

Configure the PWM channel:

Example: Set PWM0_CH1 channel, with a period of 10000ns, a duty cycle of 5000ns, and the polarity as normal.

```
$ echo 0 > /sys/class/pwm/pwmchip0/export
$ echo 10000 > /sys/class/pwm/pwmchip0/pwm0/period
$ echo 5000 > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
$ echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
$ echo 1 > /sys/class/pwm/pwmchip0/pwm0/enable
```

> After the configuration parameters are successfully set according to the example, you can use a multimeter to measure the PWM0 pin. The correct voltage should be around 1.6V.

Export the PWM channel:

```
echo 0 > /sys/class/pwm/pwmchip0/export
```

Set the PWM period (frequency):

```
echo x > /sys/class/pwm/pwmchip0/pwm0/period
```

Set the PWM duty cycle:

```
echo x > /sys/class/pwm/pwmchip0/pwm0/duty_cycle
```

Set the PWM polarity:

```
echo normal > /sys/class/pwm/pwmchip0/pwm0/polarity
echo inverted > /sys/class/pwm/pwmchip0/pwm0/polarity
```

Enable PWM output:

```
echo 1/0 > /sys/class/pwm/pwmchip0/pwm0/enable
```
