# GPIO



## 文档参考

```
Linux_GPIO_开发指南.pdf
```



## DTS

Android SDK

```
longan/device/config/chips/a133/configs/c3/kickpi-k5.dts  
longan/device/config/chips/a133/configs/c3/kickpi-k5c.dts
```

Linux SDK

```
device/config/chips/a133/configs/c3/kickpi-k5.dts  
device/config/chips/a133/configs/c3/kickpi-k5c.dts
```



sunxi



dts gpios 配置解析

```
gpios = <&pio   PH 0x8 0x0 0x1 0x0 0x1>;
gpios = <&r_pio PL 0x4 0x0 0x1 0x0 0x1>;
gpios = <&A		B  C   D   E   F   G>;
```

> `&A` &pio/&r_pio 指定 pio，属于 cpus 要用 &r_pio （PL之后的）
>
> `B` PH/PL  指定 gpio 的 bank
>
> `C` 0x8/0x4 指定 bank 中的哪个引脚
>
> `D` 复用类型
>
> `E` 上下拉，值为 0x1 时采用默认值
>
> `F` 驱动能力，值为 0x0 时采用默认值
>
> `G` 输出电平，只用 output 才有效



## PIN 控制

### sys gpio控制

当存在将拓展引脚配置为输入的需求，默认软件的gpio-led不能满足需求。需要将GPIO释放出来，通过/sys/class/gpio进行控制。

步骤一

先将对应GPIO引脚注释，/sys/class/gpio/export 只能导入未注册的 gpio

比如PH8，如需其他引脚，一样在 leds 结点下注释即可 ,  下面内容都以PH8 做举例

```diff
vim device/config/chips/a133/configs/c3/kickpi-k5c.dts
+ /*
			PH8 {
				label = "PH8";
				gpios = <&pio PH 8 1 0 1 0>;
				linux,default_trigger = "default-on";
				default-state = "on";
			};
+ */
```

步骤二

​		编译镜像，重新烧录

步骤三

​		确认gpio未被注册

```
root@kickpi:~# cat /sys/kernel/debug/pinctrl/pio/pinmux-pins  | grep PH8
pin 232 (PH8): (MUX UNCLAIMED) (GPIO UNCLAIMED)
```

​		能够看到PH8未被使用，且对应 pin 值为 232 

步骤四

​		通过 /sys/class/gpio/export 注册 PH8 并进行控制

```
// 注册
root@kickpi:~# echo  232 > /sys/class/gpio/export
// 查看是否生成
root@kickpi:~# ls /sys/class/gpio/
export  gpio232  gpiochip0  gpiochip352  unexport
// 注册后的结点内容
root@kickpi:~# ls /sys/class/gpio/gpio232
active_low  device  direction  edge  power  subsystem  uevent  value
root@kickpi:~#
```

​			通过结点下的内容控制 gpio , 常用如下

```
direction
	in / out
	echo in > /sys/class/gpio/gpio232/direction
	echo out > /sys/class/gpio/gpio232/direction
value
	0 / 1
	cat /sys/class/gpio/gpio232/value 		// 读取
	echo 1 > /sys/class/gpio/gpio232/value	// 配置高电平
	echo 0 > /sys/class/gpio/gpio232/value  // 配置低电平
```



### sunxi gpio debug 控制

```
cd /sys/kernel/debug/sunxi_pinctrl
```
查看 pin 的配置
```
# cd /sys/kernel/debug/sunxi_pinctrl
# echo PH8 > sunxi_pin
# cat sunxi_pin_configure
```
配置 pin 为 gpio 模式
```
# cd /sys/kernel/debug/sunxi_pinctrl
# echo 'PH8 0' > function
```
修改 pin 的上拉属性
```
# cd /sys/kernel/debug/sunxi_pinctrl
# echo 'PH8 1' > pull
```
查看修改情况
```
# cd /sys/kernel/debug/sunxi_pinctrl
# cat pull					
# cat sunxi_pin_configure
```

查看 pin 电平
```
# cd /sys/kernel/debug/sunxi_pinctrl
# cat data
```

> 注意：
>
> 切换 pio
>
> ```
> echo pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
> cat /sys/kernel/debug/sunxi_pinctrl/dev_name
> ```
>
> 切换 r_pio （当操作PL及之后的pin ， 需要切换 r_pio 的设备， 否则操作失败）
>
> ```
> echo r_pio > /sys/kernel/debug/sunxi_pinctrl/dev_name
> cat /sys/kernel/debug/sunxi_pinctrl/dev_name
> ```

