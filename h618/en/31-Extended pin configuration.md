# 31-Extended pin configuration





## profile path

Extended pin configuration, only need to modify the device tree file, the following examples are Android, Linux SDK corresponding device tree path



**Android SDK device tree path**

```
$ vim longan/device/config/chips/h618/configs/p2/board.dts
```

**Linux SDK device tree path**

```
$ vim source/kernel/linux-5.4-h618/arch/arm64/boot/dts/sunxi/sun50iw9-lubancat-a1.dts
```





## sample feature configuration



### GPIO output

The K2B development board is configured with two GPIO output functions by default, namely PC12 (12 pins) and PC7 (14 pins). Note: Other pins have been configured for other functions. If you want to configure them as GPIO functions, you need to remove the GPIO output from the default function and provide two control methods, namely gpio-leds, gpio_para

**gpio-leds**

GPIO-leds is the GPIO control driver that comes with the LINUX kernel, which can realize rich control methods. The following example is the system running indicator configuration to realize the heartbeat flashing function

```
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



**gpio_para**

GPIO-para is a GPIO control driver for Allwinner, which can realize simple level control. 

The following example is the configuration of the expansion pins PC7 and PC12

```
&soc {
        gpio_para {
                gpio_num = <2>;
                gpio_pin_1 = <&pio PC 7 GPIO_ACTIVE_HIGH>;
                gpio_pin_2 = <&pio PC 12 GPIO_ACTIVE_HIGH>;
                status = "okay";
        };
};
```





### GPIO key

```
/{
		gpio-keys {
                compatible = "gpio-keys";
                status = "okay";
                autorepeat;

                power {
                        label = "Power Key";
                        linux,code = <KEY_POWER>;
                        gpio = <&pio PC 2 GPIO_ACTIVE_LOW>;     /* PC7 */
                        wakeup-source;
                        debounce-interval = <100>;
                };
        };
};
```

