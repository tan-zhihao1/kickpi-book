# LRADC

## LRADC

位置：开发板 20Pin [扩展引脚](扩展引脚) 1 号脚

* 低速率ADC（LRADC）
* 6位分辨率
* 采样率高达2 kHz
* 支持保持键和通用键
* 支持正常、继续和单一工作模式
* 电源电压： 1.8 V，功率参考电压： 1.35 V，模拟输入，检测电压范围：0~LEVELB（最大值为1.266 V）



## 硬件注意事项

​		K5 目前 LRADC 为 SOC 直接引出引脚，ADC电路设计需要上拉电压，接入到 LRADC 最高不超过 1.266 V 。



## dts

LRADC 模块的设备树配置位于 longan 的内核目录 `longan/device/config/chips/a133/configs/c3/board.dts`

linux 路径为 `device/config/chips/a133/configs/c3/board.dts`

```c
sunxi_keyboard: keyboard {
    compatible = "allwinner,keyboard_1350mv";
	status = "okay"; /* 使能该节点 */
	key_cnt = <5>; /* 键值数量 */
	keyo = <115 115>; /*根据实际情况,左边115是电压,单位为mV,右边115为该电压对应的键值*/
	keyl = <235 114>;
	key2 = <330 139>;
	key3 = <420 28>;
	key4 = <520 102>;
};
```



键值可参考：

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



## 调试

### proc

确认查看 LRADC 的数据上报节点

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

查看linux是否上报Input事件

```
getevent
getevent -l
```



